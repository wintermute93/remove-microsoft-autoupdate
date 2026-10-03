# remove-microsoft-autoupdate

[English](README.md) | **Português (Brasil)** | [Русский](README.ru.md)

Um script shell pequeno que remove o Microsoft AutoUpdate (MAU) do macOS.

## Por quê

Basta instalar Word, Excel, Teams, Edge ou qualquer outro app da Microsoft no
Mac para o Microsoft AutoUpdate vir junto, você querendo ou não. A partir
daí ele:

- abre no login e fica rodando em segundo plano;
- mostra janelas de atualização nas piores horas;
- instala uma ferramenta auxiliar que roda como root
  (`com.microsoft.autoupdate.helper`), que já teve falhas de escalonamento de
  privilégio no passado;
- baixa centenas de megabytes de atualizações quando bem entende;
- volta a funcionar mesmo depois de você desligar as atualizações
  automáticas nas configurações dele.

Se você prefere decidir quando o Office é atualizado, ou simplesmente não
quer mais um serviço da Microsoft rodando em segundo plano, não existe
desinstalador para o MAU. É preciso encontrar os arquivos e apagar um por
um. Este script faz isso por você.

## O que faz

1. Lista todos os arquivos do MAU que conhece, com o tamanho, para você ver
   o que existe antes de qualquer coisa ser alterada.
2. Pede confirmação.
3. Descarrega o launch daemon e o agent e encerra qualquer processo do MAU
   em execução.
4. Apaga o app, a ferramenta auxiliar, os plists do launchd, caches e
   preferências, tanto do sistema quanto do usuário atual.
5. Remove o recibo de instalação do MAU (`pkgutil --forget`).

Caminhos removidos:

```
/Library/Application Support/Microsoft/MAU2.0
/Library/LaunchAgents/com.microsoft.update.agent.plist
/Library/LaunchDaemons/com.microsoft.autoupdate.helper.plist
/Library/PrivilegedHelperTools/com.microsoft.autoupdate.helper
/Library/Preferences/com.microsoft.autoupdate2.plist
/Library/Caches/com.microsoft.autoupdate.helper
~/Library/Preferences/com.microsoft.autoupdate2.plist
~/Library/Preferences/com.microsoft.autoupdate.fba.plist
~/Library/Caches/com.microsoft.autoupdate2
~/Library/Caches/com.microsoft.autoupdate.fba
~/Library/HTTPStorages/com.microsoft.autoupdate2
~/Library/Application Support/Microsoft AU Daemon
```

O Office em si não é tocado. Word, Excel, PowerPoint, Outlook e os demais
continuam funcionando normalmente, só deixam de se atualizar sozinhos.

## Uso

```sh
git clone https://github.com/wintermute93/remove-microsoft-autoupdate.git
cd remove-microsoft-autoupdate

./remove-microsoft-autoupdate --dry-run   # mostra o que seria removido
sudo ./remove-microsoft-autoupdate
```

Opções:

```
-n, --dry-run   lista o que seria removido, sem alterar nada
-y, --yes       não pede confirmação
-h, --help      mostra a ajuda
-V, --version   mostra a versão
```

Para deixar instalado no sistema:

```sh
sudo install -m 755 remove-microsoft-autoupdate /usr/local/bin/
```

## Observações

- Instalar ou atualizar um app do Office pelo instalador `.pkg` da Microsoft
  traz o MAU de volta. Basta rodar o script de novo depois.
- Sem o MAU, o Office não se atualiza sozinho. As atualizações estão na
  página [Office for Mac update history](https://learn.microsoft.com/officeupdates/update-history-office-for-mac),
  que também tem o instalador avulso do MAU, caso você queira ele de volta.
- O Office instalado pela Mac App Store é atualizado pela própria App Store
  e não depende do MAU.

## Licença

MIT
