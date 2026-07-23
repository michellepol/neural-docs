# Yubikey ssh аутентификация для github

## Требования
- YubiKey с поддержкой **FIDO2** (обычно YubiKey 5 / Bio).
- OpenSSH с поддержкой `*-sk` ключей (обычно OpenSSH **8.2+**).

### Утилита управления YubiKey:
- **Linux:** `sudo apt install yubikey-manager`
- **macOS (brew):** `brew install yubikey-manager`
- **Windows:** поставь “YubiKey Manager” (GUI) или `ykman` (CLI)

---

## 1. (Опционально) Задать/сменить PIN для FIDO2 на YubiKey
Можно сделать через `ykman` (YubiKey Manager CLI), но не обязательно: `ssh-keygen` тоже предложит задать PIN при первом использовании.

Если есть `ykman`:
```bash
ykman fido access change-pin
```

Проверить состояние можно так:
```bash
ykman fido info
```

---

## 2. Сгенерировать SSH-ключ, который хранится на YubiKey
Рекомендую **resident key** (ключ “живет” на YubiKey; можно восстановить на другом ПК) и требование PIN.

```bash
ssh-keygen -t ed25519-sk -O resident -O verify-required -O application=ssh:github -C "git-yubikey" -f ~/.ssh/id_ed25519_sk_yk
```

Что будет происходить:
- попросит **коснуться** YubiKey;
- попросит **PIN** (и предложит создать, если его нет);
- создаст 2 файла:
  - `~/.ssh/id_ed25519_sk_yk` — *это не приватник*, а “ссылка/handle” на ключ в YubiKey
  - `~/.ssh/id_ed25519_sk_yk.pub` — публичный ключ

> Если не хотите вводить PIN при каждом пуше, можно убрать `-O verify-required` (останется только “touch”). Но с PIN безопаснее.

---

## 3. Добавить публичный ключ в Git-хостинг (GitHub/GitLab/и т.д.)
Скопируйте публичный ключ:
```bash
cat ~/.ssh/id_ed25519_sk_yk.pub
```

---

## 4. Настроить SSH, чтобы Git точно использовал этот ключ
Откройте/создайте `~/.ssh/config`:

### Для GitHub
```sshconfig
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_sk_yk
  IdentitiesOnly yes
```

### Для GitLab (если нужно)
```sshconfig
Host gitlab.com
  HostName gitlab.com
  User git
  IdentityFile ~/.ssh/id_ed25519_sk_yk
  IdentitiesOnly yes
```

Права на конфиг (Linux/macOS):
```bash
chmod 600 ~/.ssh/config
```

---

## 5. Убедиться, что remote в репозитории — SSH, а не HTTPS
Проверьте:
```bash
git remote -v
```

Если там `https://...`, поменяйте на SSH. Например для GitHub:
```bash
git remote set-url origin git@github.com:USER/REPO.git
```

---

## 6. Проверить SSH-авторизацию
### GitHub
```bash
ssh -T git@github.com
```

Должно попросить PIN/touch и затем сказать, что вы успешно аутентифицировались.

---

## 8. Использование ключей на другом компьютере

Если вы сделали `-O resident`, то на новом компьютере можно восстановить “видимость” ключа:
   ```bash
   ssh-keygen -K
   ```

   (вытянет публичные ключи/дескрипторы резидентных ключей с YubiKey в `~/.ssh/`)

# Частые проблемы и быстрые решения

## 1) “Permission denied (publickey)”
- Проверь, что remote именно SSH: `git remote -v`
- Проверь, что ключ добавлен в GitHub/GitLab (именно `.pub`)
- Проверь, что используется правильный ключ:
  ```bash
  ssh -vT git@github.com
  ```
  По логу видно, какой `IdentityFile` берётся.

