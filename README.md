## Заметки о работе с Vault
![scheme](images/Secrets+management+-+Vault.jpg)
Кратко:
- Основной плюс, это отзыв secrets
- ACL не привычный, но относительно понятный
- all DENY policy - примерно как и в mikrotik, запрещено все, что не разрешено явно

## Концепции Vault
### Sealing (Запечатывание)/Unsealing (Распечатывание) Vault
![key](images/keyvalue.jpg)
Информация, которую вы храните в Vault, защищается мастер-ключом. Vault использует его, чтобы кодировать/декодировать секреты. Мастер-ключ сам по себе недоступен и реконструируется каждый раз, когда вы запускаете/перезапускаете Vault.
Чтобы всё работало надёжно, Vault использует алгоритм Shamir’s Secret Sharing (Схема разделения секрета Шамира).

Мастер-ключ по-умолчанию разделяется на 5 ключей — это называется **number of shares** (число сторон).

Для реконструкции мастер-ключа вам понадобится предоставить любые 3 из 5 ключей — это называется **threshold** (порог)

## Root токен

![Ключи](images/joz9gz.jpg)

### Первоначальная инициализация хранилища

```
root@vault:~# vault status
Key                Value
---                -----
Seal Type          shamir
Initialized        false
Sealed             true
Total Shares       0
Threshold          0
Unseal Progress    0/0
Unseal Nonce       n/a
Version            1.9.2
Storage Type       file
HA Enabled         false

```
** Sealed : true **
Это означает, что хранилище запечатано. Для его открытия, нужно призывать хранителей ключей (по дефолту, их 5, но достаточно призвать троих)

На практике - инициализация и выдача ключей, а так же распечатывание выглядит примерно так:
```
root@vault:~# vault operator init
Unseal Key 1: U6cUgPn2NbUfoRr4G0TPWI91FIlxSAHs8m7R8/SMtbha
Unseal Key 2: xHTXjC/SuldYWoSweSKs80YIVeLtddweJ9lL6XPNUeM3
Unseal Key 3: uXISe6DMGdjoSuAOtBLAjkw3G92KIdgdnXfJUMSpLQxu
Unseal Key 4: ybtwK5+rluhiGDiNqxx0WkoaNgnkaGsNgn5QzS9kQDa/
Unseal Key 5: N1jZu8AZbpepsxPfsjiP+tJQDhIBbTOgVbjMoBRVk7XE

Initial Root Token: s.Wt7VBGiG9dFKSC5mNNdwg8nm <- Его лучше не давать и не использовать нигде, ибо от СБ бо-бо
```

И собственно распечатывание хранилища
```
root@vault:~# vault operator unseal U6cUgPn2NbUfoRr4G0TPWI91FIlxSAHs8m7R8/SMtbha
Key                Value
---                -----
Sealed             true
Unseal Progress    1/3

root@vault:~# vault operator unseal xHTXjC/SuldYWoSweSKs80YIVeLtddweJ9lL6XPNUeM3
Key                Value
---                -----
Sealed             true
Unseal Progress    2/3

root@vault:~# vault operator unseal uXISe6DMGdjoSuAOtBLAjkw3G92KIdgdnXfJUMSpLQxu
Key             Value
---             -----
Sealed          false
```
![open](/images/open.jpg)