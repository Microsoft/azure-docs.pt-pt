---
title: Configure Ferramenta instantânea consistente de aplicação Azure para ficheiros Azure NetApp | Microsoft Docs
description: Fornece um guia para executar o comando de configuração da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632976"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Configure Aplicação Azure Ferramenta Snapshot consistente (pré-visualização)

Este artigo fornece um guia para executar o comando de configuração da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.

## <a name="introduction"></a>Introdução

O ficheiro de configuração pode ser criado ou editado utilizando o `azacsnap -c configure` comando.

## <a name="command-options"></a>Opções de comando

O `-c configure` comando tem as seguintes opções

- `--configuration new` para criar um novo ficheiro de configuração.

- `--configuration edit` para editar um ficheiro de configuração existente.

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de ficheiros de configuração personalizados.

## <a name="configuration-file-for-snapshot-tools"></a>Ficheiro de configuração para ferramentas instantâneas

Um ficheiro de configuração pode ser criado executando `azacsnap -c configure --configuration new` .  Por predefinição, o nome de ficheiro de configuração é `azacsnap.json` .  Um nome de ficheiro personalizado pode ser usado com o `--configfile=` parâmetro (por exemplo, `--configfile=<customname>.json` ) O exemplo a seguir é para a configuração Azure Large Instance:

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>Detalhes dos valores exigidos

As seguintes secções fornecem orientações detalhadas sobre os vários valores necessários para o ficheiro de configuração.

### <a name="sap-hana-values"></a>Valores SAP HANA

Ao adicionar uma *base de dados* à configuração, são necessários os seguintes valores:

- **Endereço do servidor HANA** = O nome de anfitrião do servidor SAP HANA ou endereço IP.
- **HANA SID** = O ID do Sistema SAP HANA.
- **Número de exemplo HANA** = O número de instância SAP HANA.
- **Tecla HANA HDB User Store** = O utilizador SAP HANA configurado com permissões para executar cópias de segurança de base de dados.

- Nó único: IP e nome de anfitrião do nó
- HSR com STONITH: IP e nome de anfitrião do nó
- Scale-out (N+N, N+M): Atual nó principal IP e nome de anfitrião
- HSR sem STONITH: IP e nome de anfitrião do nó
- Multi SID no nó único: Nome de anfitrião e IP do nó que hospeda esses SIDs

### <a name="azure-large-instance-hli-storage-values"></a>Valores de armazenamento Azure Large Instance (HLI)

Ao adicionar *armazenamento HLI* a uma secção de base de dados, são necessários os seguintes valores:

- **Nome do utilizador do armazenamento** = Este valor é o nome de utilizador utilizado para estabelecer a ligação SSH ao Armazenamento.
- **Endereço IP de armazenamento** = O endereço do sistema de armazenamento.
- **Nome do volume de** armazenamento = o nome do volume para instantâneo.  Este valor pode ser determinado de várias maneiras, talvez o mais simples seja experimentar o seguinte comando de concha:

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Valores de armazenamento de Ficheiros Azure NetApp (ANF)

Ao adicionar *armazenamento ANF* a uma secção de base de dados, são necessários os seguintes valores:

- **Nome de ficheiro de autenticação principal** do serviço = este é o `authfile.json` ficheiro gerado na Cloud Shell ao configurar a comunicação com o armazenamento de Ficheiros Azure NetApp.
- **ID de volume de armazenamento total ANF** = o ID completo do recurso do volume sendo instantâneo.  Isto pode ser recuperado a partir de: Azure portal –> ANF – Volume > – Definições/Propriedades > –ID de recursos >

## <a name="configuration-file-overview-azacsnapjson"></a>Visão geral do ficheiro de configuração `azacsnap.json` ()

No exemplo seguinte, o `azacsnap.json` é configurado com um SID.

Os valores dos parâmetros devem ser definidos para o ambiente SAP HANA específico do cliente.
Para o sistema **Azure Large Instance,** estas informações são fornecidas pela Microsoft Service Management durante a chamada de embarque/entrega, e são disponibilizadas num ficheiro Excel que é fornecido durante a entrega. Abra um pedido de serviço se precisar de ser novamente fornecido.

O seguinte é apenas um exemplo, atualizar todos os valores em conformidade.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
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

> [!NOTE]
> Para um cenário DR em que as cópias de segurança devem ser executadas no site DR, então o Nome do Servidor HANA configurado no ficheiro de configuração DR (por exemplo, `DR.json` ) no site DR deve ser o mesmo que o nome do servidor de produção.

> [!NOTE]
> Para a Azure Large Instance, o seu endereço IP de armazenamento deve estar na mesma sub-rede que a piscina do servidor. Por exemplo, neste caso, a nossa sub-rede de piscina de servidor é 172. 18. 18.0/24 e o nosso IP de armazenamento atribuído é 172.18.18.11.

## <a name="next-steps"></a>Passos seguintes

- [Teste Azure Aplicação Consistente Ferramenta Snapshot](azacsnap-cmd-ref-test.md)
