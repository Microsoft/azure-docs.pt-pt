---
title: Dicas e truques para usar a ferramenta Azure Application Consistent Snapshot para ficheiros Azure NetApp | Microsoft Docs
description: Fornece dicas e truques para a utilização da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 6465acc0d4ce760e0bf89c73dace7c8c66d37c49
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869944"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Dicas e truques para utilizar a ferramenta Azure Application Consistent Snapshot (pré-visualização)

Este artigo fornece dicas e truques que podem ser úteis quando você usa AzAcSnap.

## <a name="limit-service-principal-permissions"></a>Limitar permissões principais de serviço

Pode ser necessário limitar o âmbito de aplicação do serviço AzAcSnap.  Reveja a [documentação do Azure RBAC](../role-based-access-control/index.yml) para obter mais detalhes sobre a gestão de acessos finos dos recursos da Azure.  

Segue-se uma definição de função de exemplo com as ações mínimas necessárias para que o AzAcSnap funcione.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

Para que as opções de restauro funcionem com sucesso, o diretor de serviço AzAcSnap também precisa de ser capaz de criar volumes.  Neste caso, a definição de função necessita de uma ação adicional, pelo que o diretor de serviço completo deve parecer o exemplo seguinte.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>Tire fotos manualmente

Antes de executar quaisquer comandos de backup `azacsnap -c backup` (), verifique a configuração executando os comandos de teste e verifique se são executados com sucesso.  A correta execução destes testes `azacsnap` comprovados pode comunicar com a base de dados SAP HANA instalada e com o sistema de armazenamento subjacente do SAP HANA no **sistema Azure Large Instance** ou **Azure NetApp Files.**

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

Em seguida, para fazer uma cópia de segurança manual de imagens da base de dados, executar o seguinte comando:

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Configurar cópia de segurança automática do instantâneo

É prática comum nos sistemas Unix/Linux utilizar `cron` para automatizar comandos de execução num sistema. A prática padrão para as ferramentas instantâneas é configurar a do utilizador `crontab` .

Um exemplo de um `crontab` para o utilizador `azacsnap` automatizar fotografias está abaixo.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

Explicação do crontab acima.

- `MAILTO=""`: ao ter um valor vazio, isto impede que a Cron tente enviar automaticamente um e-mail ao utilizador ao executar a entrada de crontab, uma vez que provavelmente acabaria no ficheiro de correio do utilizador local.
- As versões abreviadas do tempo para as entradas de crontab são autoexplicativas:
  - `@monthly` = Corra uma vez por mês, ou seja, "0 0 1 **".
  - `@weekly`  = Corra uma vez por semana, ou seja, "0 0 * * 0".
  - `@daily`   = Corra uma vez por dia, ou seja, "0 0 * **".
  - `@hourly`  = Corra uma vez por hora, ou seja, "0* **".
- As primeiras cinco colunas são usadas para designar tempos, referem-se aos exemplos de colunas abaixo:
  - `0,15,30,45`: A cada 15 minutos
  - `0-23`: A cada hora
  - `*` : Todos os dias
  - `*` : Todos os meses
  - `*` : Todos os dias da semana
- Linha de comando para executar incluída dentro dos parênteses "()"
  - `. /home/azacsnap/.profile` = puxe o perfil .do utilizador para configurar o seu ambiente, incluindo $PATH, etc.
  - `cd /home/azacsnap/bin` = alterar o diretório de execução para o local "/home/azacsnap/bin" onde estão os ficheiros config.
  - `azacsnap -c .....` = o comando completo de azacsnap para executar, incluindo todas as opções.

Explicação adicional do cron e do formato do ficheiro crontab aqui: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> Os utilizadores são responsáveis pela monitorização dos crons para garantir que os instantâneos estão a ser gerados com sucesso.

## <a name="monitor-the-snapshots"></a>Monitorize as fotos

Devem ser controladas as seguintes condições para assegurar um sistema saudável:

1. Espaço disponível em disco. As imagens consumirão lentamente o espaço do disco, uma vez que manter os blocos de disco mais antigos são retidos no instantâneo.
    1. Para ajudar a automatizar a gestão do espaço em disco, utilize as `--retention` opções e `--trim` opções para limpar automaticamente as fotos antigas e os ficheiros de registo de bases de dados.
1. Execução bem sucedida das ferramentas instantâneas
    1. Verifique se o `*.result` ficheiro é o sucesso ou falha da última execução de `azacsnap` .
    1. Verifique a `/var/log/messages` saída do `azacsnap` comando.
1. Consistência dos instantâneos devolvendo-os periodicamente a outro sistema.

> [!NOTE]
> Para listar detalhes de instantâneo, execute o comando `azacsnap -c details` .

## <a name="delete-a-snapshot"></a>Apagar um instantâneo

Para eliminar uma imagem, execute o comando `azacsnap -c delete` . Não é possível apagar instantâneos do nível de SO. Tem de utilizar o comando correto `azacsnap -c delete` para eliminar as imagens de armazenamento.

> [!IMPORTANT]
> Esteja atento quando apagar uma fotografia. Uma vez eliminados, é **IMPOSSÍVEL** recuperar as imagens apagadas.

## <a name="restore-a-snapshot"></a>Restaurar um instantâneo

Um instantâneo de volume de armazenamento pode ser restaurado para um novo volume ( `-c restore --restore snaptovol` ).  Para Azure Large Instance, o volume pode ser revertido para um instantâneo ( `-c restore --restore revertvolume` ).

> [!NOTE]
> Não **existe** um comando de recuperação de base de dados fornecido.

Um instantâneo pode ser copiado de volta para a área de dados SAP HANA, mas SAP HANA não deve estar a funcionar quando uma cópia é feita ( `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` ).

Para a Azure Large Instance, pode contactar a equipa de operações da Microsoft abrindo um pedido de serviço para restaurar uma imagem desejada a partir das imagens existentes. Pode abrir um pedido de serviço a partir do portal Azure: <https://portal.azure.com>

Se decidir executar o failover de recuperação de `azacsnap -c restore --restore revertvolume` desastres, o comando no site DR disponibilizará automaticamente as imagens de volume mais recentes ( `/hana/data` `/hana/logbackups` e) instantâneas de volume para permitir uma recuperação do SAP HANA. Utilize este comando com cuidado, uma vez que quebra a replicação entre os locais de produção e DR.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Configurar instantâneos apenas para volumes de 'boot'

> [!IMPORTANT]
> Esta operação aplica-se apenas à Azure Large Instance.

Em alguns casos, os clientes já têm ferramentas para proteger o SAP HANA e só querem configurar instantâneos de volume de 'boot'.  Neste caso, a tarefa é simplificada e devem ser tomadas as seguintes medidas.

1. Passos completos 1-4 dos pré-requisitos para a instalação.
1. Ativar a comunicação com o armazenamento.
1. Descarregue a execução do instalador para instalar as ferramentas instantâneas.
1. Configuração completa de ferramentas instantâneas.
1. Crie um novo ficheiro de configuração da seguinte forma. Os detalhes do volume de arranque devem estar na estrofe OtherVolume (entradas do utilizador a <span style="color:red">vermelho):</span>
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Verifique o ficheiro config, consulte o seguinte exemplo:

    Utilize `cat` o comando para exibir o conteúdo do ficheiro de configuração:

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. Teste uma cópia de segurança do volume da bota

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Verifique se está listado, note a adição da `--snapshotfilter` opção de limitar a lista de instantâneos devolvida.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Saída de comando:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Configurar a cópia de segurança automática do instantâneo.

> [!NOTE]
> Não é necessária a criação de comunicação com o SAP HANA.

## <a name="restore-a-boot-snapshot"></a>Restaurar uma foto de 'boot'

> [!IMPORTANT]
> Esta operação é para a Azure Large Instance ony.
> O Servidor será restaurado ao ponto de o Instantâneo ter sido tirado.

Um instantâneo 'boot' pode ser recuperado da seguinte forma:

1. O cliente terá de desligar o servidor.
1. Depois de o Servidor ser desligado, o cliente terá de abrir um pedido de serviço que contenha o ID da Máquina e o Snapshot para restaurar.
    > Os clientes podem abrir um pedido de serviço a partir do portal Azure: <https://portal.azure.com>
1. A Microsoft restaurará o Sistema Operativo LUN utilizando o ID e Instantâneo da máquina especificados e, em seguida, iniciará o Servidor.
1. O cliente terá então de confirmar que o Server é iniciado e saudável.

Não há passos adicionais a serem realizados após a restauração.

## <a name="key-facts-to-know-about-snapshots"></a>Principais factos para saber sobre instantâneos

Principais atributos de instantâneos de volume de armazenamento:

- **Localização dos instantâneos**: As imagens podem ser encontradas num diretório virtual `.snapshot` dentro do volume.  Consulte os seguintes exemplos para **Azure Large Instance**:
  - Base de dados: `/hana/data/<SID>/mnt00001/.snapshot`
  - Partilhado: `/hana/shared/<SID>/.snapshot`
  - Registos: `/hana/logbackups/<SID>/.snapshot`
  - Boot: as fotos de arranque para HLI não são **visíveis** a partir do nível de SO, mas podem ser listadas usando `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` é uma pasta *virtual* escondida apenas para leitura que fornece acesso apenas de leitura aos instantâneos.

- **Foto máxima:** O hardware pode sustentar até 250 instantâneos por volume. O comando snapshot manterá um número máximo de instantâneos para o prefixo com base no conjunto de retenção definido na linha de comando, e apagará o instantâneo mais antigo se for além do número máximo para reter.
- **Nome do instantâneo:** O nome snapshot inclui a etiqueta de prefixo fornecida pelo cliente.
- **Tamanho do instantâneo:** Depende do tamanho/alterações no nível de base de dados.
- **Localização do ficheiro de registo:** Os ficheiros de registo gerados pelos comandos são saídas em pastas tal como definidas no ficheiro de configuração JSON, que por predefinição é uma sub-dobragem sob a qual o comando é executado (por exemplo, `./logs` ).

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas](azacsnap-troubleshoot.md)
