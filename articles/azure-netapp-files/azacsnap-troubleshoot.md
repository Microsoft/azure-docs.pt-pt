---
title: Resolução de problemas Azure Aplicação Consistente Ferramenta Snapshot para ficheiros Azure NetApp | Microsoft Docs
description: Fornece conteúdo de resolução de problemas para a utilização da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.
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
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 7ba5a05515284612e17d5aba4cc673c7e78f7ba1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869927"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Resolução de problemas Azure Aplicação Consistente Ferramenta Snapshot (pré-visualização)

Este artigo fornece conteúdo de resolução de problemas para a utilização da ferramenta Azure Application Consistent Snapshot que pode utilizar com ficheiros Azure NetApp.

Seguem-se questões comuns que poderá encontrar durante a execução dos comandos. Siga as instruções de resolução mencionadas para corrigir o problema. Se ainda encontrar um problema, abra um Pedido de Serviço do portal Azure e atribua o pedido na fila SAP HANA Large Instance para o Microsoft Support responder.

## <a name="log-files"></a>Ficheiros de registo

Uma das melhores fontes de informação para depurar quaisquer erros com o AzAcSnap são os ficheiros de registo.  

### <a name="log-file-location"></a>Localização do ficheiro de registo

Os ficheiros de registo são armazenados no diretório configurado de acordo com o `logPath` parâmetro do ficheiro de configuração AzAcSnap.  O nome de ficheiro de configuração padrão é `azacsnap.json` e o valor padrão é o `logPath` que significa que os `"./logs"` ficheiros de registo são escritos no `./logs` diretório relativamente ao local onde o `azacsnap` comando é executado.  A `logPath` localização absoluta (por `/home/azacsnap/logs` exemplo) assegurará `azacsnap` as saídas dos registos, `/home/azacsnap/logs` independentemente do local onde o `azacsnap` comando foi executado.

### <a name="log-file-naming"></a>Nomeação de ficheiro de registo

O nome de ficheiro de registo baseia-se no nome da aplicação (por `azacsnap` exemplo), na opção de comando `-c` (por `backup` exemplo, `test` , `details` etc.) e no nome de ficheiro de configuração (por exemplo, padrão = `azacsnap.json` ).  Assim, se utilizar o `-c backup` comando, o nome de ficheiro de registo por predefinição seria `azacsnap-backup-azacsnap.log` e está escrito no diretório configurado por `logPath` .  

Esta convenção de nomeação foi estabelecida para permitir vários ficheiros de configuração, um por base de dados, e garantir a facilidade de localizar os ficheiros de logfiles associados.  Portanto, se o nome de ficheiro de configuração for `SID.json` , então o nome de dados de resultados ao utilizar as `azacsnap -c backup --configfile SID.json` opções será `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>Arquivo de resultados e syslog

Para a `-c backup` opção de comando, o AzAcSnap escreve para um `*.result` ficheiro e o registo do sistema ( ) utilizando o `/var/log/messages` `logger` comando.  O `*.result` nome de ficheiro tem o mesmo nome base que o ficheiro de [registo](#log-file-naming) e vai para o mesmo local que o ficheiro [de registo](#log-file-location).  É um simples ficheiro de saída de uma linha segundo os seguintes exemplos.

Exemplo de saída a partir de `*.result` ficheiro.
```output
Database # 1 (PR1) : completed ok
```

Exemplo de saída a partir de `/var/log/messages` ficheiro.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Comunicação falhada com ficheiros Azure NetApp

Ao validar a comunicação com os Ficheiros Azure NetApp, a comunicação pode falhar ou sair do prazo.  Verifique se as regras de firewall não estão a bloquear o tráfego de saída do sistema que executa o AzAcSnap para os seguintes endereços e portas TCP/IP:-

- (https://)management.azure.com:443
- (https://)login.microsoftonline.com:443 

## <a name="failed-communication-with-sap-hana"></a>Comunicação falhada com a SAP HANA

Ao validar a comunicação com o SAP HANA executando um teste `azacsnap -c test --test hana` com o seguinte erro:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Solução:**

1. Verifique o ficheiro de configuração (por exemplo, `azacsnap.json` ) para cada instância HANA para garantir que os valores da base de dados SAP HANA estão corretos.
1. Tente executar o comando abaixo para verificar se o `hdbsql` comando está no caminho e pode ligar-se ao Servidor SAP HANA. O exemplo a seguir mostra o correto funcionamento do comando e a sua saída.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    Neste exemplo, o `hdbsql` comando não está nos utilizadores. `$PATH`

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    Neste exemplo, o `hdbsql` comando é temporariamente adicionado ao do utilizador, mas quando o `$PATH` funcionamento mostra que a chave de ligação não foi configurada corretamente com o `hdbuserstore Set` comando (consulte o guia 'Iniciar-se' para mais detalhes):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > Para adicionar permanentemente ao `$PATH` utilizador, atualize o ficheiro do utilizador `$HOME/.profile`

## <a name="the-hdbuserstore-location"></a>A `hdbuserstore` localização

Ao configurar a comunicação com o SAP HANA, o `hdbuserstore` programa é utilizado para criar as definições de comunicação seguras.  O `hdbuserstore` programa é geralmente encontrado em ou `/usr/sap/<SID>/SYS/exe/hdb/` `/usr/sap/hdbclient` .  Normalmente, o instalador adiciona a localização correta à `azacsnap` do utilizador `$PATH` .

## <a name="failed-test-with-storage"></a>Teste falhado com armazenamento

O comando `azacsnap -c test --test storage` não completa com sucesso.

### <a name="azure-large-instance"></a>Azure Grande Instância

O exemplo a seguir é de correr `azacsnap` em SAP HANA em Azure Large Instance:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Solução:** O erro acima normalmente aparece quando o utilizador de armazenamento Azure Large Instance não tem acesso ao armazenamento subjacente. Para validar o acesso ao armazenamento com o utilizador de armazenamento fornecido, execute o `ssh` comando para validar a comunicação com a plataforma de armazenamento.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Um exemplo com a saída esperada:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>Não é possível estabelecer a autenticidade do hospedeiro '172.18.18.11 (172.18.18.11)»

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Solução:** Não selecione Sim. Certifique-se de que o seu endereço IP de armazenamento está correto. Se ainda houver algum problema, confirme o endereço IP de armazenamento com a equipa de operações da Microsoft.

### <a name="azure-netapp-files"></a>Azure NetApp Files

O exemplo a seguir é de funcionar `azacsnap` num VM utilizando ficheiros Azure NetApp:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Solução:**

1. Verifique a existência do ficheiro Principal de Serviço, `azureauth.json` conforme definido no ficheiro de `azacsnap.json` configuração.
1. Verifique o ficheiro de registo (por exemplo, `logs/azacsnap-test-azacsnap.log` ) para ver se o Diretor de Serviço ( ) tem o conteúdo `azureauth.json` correto.  Exemplo do registo da seguinte forma:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Verifique o ficheiro de registo (por exemplo, `logs/azacsnap-test-azacsnap.log` ) para ver se o Diretor de Serviço ( ) `azureauth.json` expirou. Exemplo do registo da seguinte forma:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Passos seguintes

- [Sugestões](azacsnap-tips.md)
