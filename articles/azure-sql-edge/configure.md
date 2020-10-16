---
title: Configure Azure SQL Edge
description: Saiba como configurar a Borda Azure SQL.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 6284e85d8c4e9ad9f9896081f04c6b7669b8e1c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446950"
---
# <a name="configure-azure-sql-edge"></a>Configure Azure SQL Edge

Azure SQL Edge suporta a configuração através de uma das duas opções seguintes:

- Variáveis de ambiente
- Um ficheiro mssql.conf colocado na pasta /var/opt/mssql

> [!NOTE]
> A definição de variáveis ambientais substitui as definições especificadas no ficheiro mssql.conf.

## <a name="configure-by-using-environment-variables"></a>Configure usando variáveis ambientais

O Azure SQL Edge expõe várias variáveis ambientais diferentes que podem ser usadas para configurar o recipiente SQL Edge. Estas variáveis ambientais são um subconjunto dos disponíveis para o SQL Server em Linux. Para obter mais informações sobre o SQL Server sobre variáveis ambientais Linux, consulte [variáveis ambientais](/sql/linux/sql-server-linux-configure-environment-variables/).

As seguintes novas variáveis ambientais foram adicionadas à Azure SQL Edge. 

| Variável de ambiente | Descrição | Valores |     
|-----|-----| ---------- | 
| **PlanId** | Especifica o Azure SQL Edge SKU para ser utilizado durante a inicialização. Esta variável ambiente só é necessária quando se implanta a Borda Azure SQL utilizando a borda Azure IoT. | **asde-developer-on-iot-edge** ou **asde-premium-on-iot-edge** | 
| **MSSQL_TELEMETRY_ENABLED** | Ativar ou desativar a recolha de dados de utilização e diagnóstico. | VERDADEIRO ou FALSO |  
| **MSSQL_TELEMETRY_DIR** | Define o directório-alvo para os ficheiros de auditoria de recolha de dados de utilização e diagnóstico. | Localização da pasta dentro do recipiente SQL Edge. Esta pasta pode ser mapeada para um volume de hospedeiro utilizando pontos de montagem ou volumes de dados. | 
| **MSSQL_PACKAGE** | Especifica a localização do pacote dacpac ou bacpac a ser implantado. | Pasta, ficheiro ou URL SAS contendo os pacotes dacpac ou bacpac. Para obter mais informações, consulte [os pacotes DEPLOY SQL Database DACPAC e BACPAC em SQL Edge](deploy-dacpac.md). |


O seguinte SQL Server na variável ambiente Linux não é suportado para Azure SQL Edge. Se definido, esta variável ambiente será ignorada durante a inicialização do recipiente.

| Variável de ambiente | Descrição |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Ativar o grupo de disponibilidade. Por exemplo, **1** está ativado e **0** está desativado. |

> [!IMPORTANT]
> A variável de ambiente **MSSQL_PID** para SQL Edge apenas aceita **Premium** e **Developer** como valores válidos. O Azure SQL Edge não suporta a inicialização utilizando uma chave de produto.

### <a name="specify-the-environment-variables"></a>Especificar as variáveis ambientais

Especifique variáveis ambientais para SQL Edge quando implementar o serviço através do [portal Azure](deploy-portal.md). Pode adicioná-las na secção **De Variáveis Ambientais** da implantação do módulo ou como parte das **Opções de Criação de Recipientes.**

Adicionar valores em **Variáveis Ambientais.**

![Definido usando a lista de variáveis ambientais](media/configure/set-environment-variables.png)

Adicione valores em **Opções de Criação de Recipientes.**

![Definir usando opções de criação de recipiente](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> No modo de implantação desligado, as variáveis ambientais podem ser especificadas utilizando a `-e` opção ou `--env` a `--env-file` opção do `docker run` comando.

## <a name="configure-by-using-an-mssqlconf-file"></a>Configure usando um ficheiro mssql.conf

O Azure SQL Edge não inclui o [utilitário de configuração mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) como o SQL Server em Linux. É necessário configurar manualmente o ficheiro mssql.conf e colocá-lo na unidade de armazenamento persistente que está mapeada para a pasta /var/opt/mssql/ no módulo SQL Edge. Quando estiver a implantar sql edge a partir do Azure Marketplace, este mapeamento é especificado como a opção **Mounts** nas **Opções de Criação**de Recipientes .

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

As seguintes novas opções mssql.conf foram adicionadas para Azure SQL Edge. 

|Opção|Descrição|
|:---|:---|
|**feedback do cliente** | Escolha se o SQL Server envia feedback para a Microsoft. Para obter mais informações, consulte [Desativar a utilização e recolha de dados de diagnóstico](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection)|      
|**userrequestedlocalauditdirectory** | Define o directório-alvo para os ficheiros de auditoria de recolha de dados de utilização e diagnóstico. Para mais informações, consulte [auditoria local de utilização e recolha de dados de diagnóstico](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection) |        

As seguintes opções mssql.conf não são aplicáveis à SQL Edge:

|Opção|Descrição|
|:---|:---|
|**Comentários dos clientes** | Escolha se o SQL Server envia feedback para a Microsoft. |
|**Perfil de correio de base de dados** | Desacrie o perfil de correio de base de dados predefinido para o SQL Server no Linux. |
|**Elevada disponibilidade** | Ativar grupos de disponibilidade. |
|**Coordenador de transações distribuídas da Microsoft** | Configure e resolva os problemas msdTC em Linux. As opções adicionais de configuração distribuídas relacionadas com transações não são suportadas para o SQL Edge. Para obter mais informações sobre estas opções de configuração adicionais, consulte [Configure MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**Serviços ML EULAs** | Aceite eulas R e Python para pacotes de aprendizagem automática Azure. Aplica-se apenas ao SQL Server 2019.|
|**saída da rede de trabalho** |Permitir o acesso à rede de saída para [extensões de Serviços de Machine Learning](/sql/linux/sql-server-linux-setup-machine-learning/) R, Python e Java.|

O ficheiro mssql.conf da amostra que se segue funciona para o SQL Edge. Para obter mais informações sobre o formato de um ficheiro mssql.conf, consulte [o formato mssql.conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Executar Azure SQL Edge como utilizador não-raiz

Por predefinição, os recipientes Azure SQL Edge funcionam com um utilizador/grupo não raiz. Quando implantados através do Azure Marketplace (ou utilizando o estivador), a menos que seja especificado um utilizador/grupo diferente, os recipientes SQL Edge começam como o utilizador mssql (não raiz). Para especificar um utilizador diferente sem raízes durante a implementação, adicione o `*"User": "<name|uid>[:<group|gid>]"*` par de valores-chave sob o recipiente criar opções. No exemplo abaixo, a SqL Edge está configurada para começar como utilizador `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Para permitir que o utilizador não-raiz aceda a ficheiros DB que se encontram em volumes montados, certifique-se de que o utilizador/grupo em que executou o recipiente, leu & escrever permissões no armazenamento persistente de ficheiros. No exemplo abaixo, definimos o utilizador sem raízes com user_id 10001 como o proprietário dos ficheiros. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Upgrade de versões ctp anteriores

Os CTPs anteriores do Azure SQL Edge foram configurados para funcionar como os utilizadores de raiz. As seguintes opções estão disponíveis ao atualizar os CTPs anteriores.

- Continue a utilizar o utilizador raiz - Para continuar a utilizar o utilizador raiz, adicione o `*"User": "0:0"*` par de valores-chave sob opções de criação de recipientes.
- Utilize o utilizador mssql predefinido - Para utilizar o utilizador mssql predefinido, siga os passos abaixo
  - Adicione um utilizador chamado MSSQl no anfitrião do estivador. No exemplo abaixo, adicionamos um mssql de utilizador com ID 10001. Este utilizador também é adicionado ao grupo raiz.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Alterar a permissão no volume de diretório/montagem onde reside o ficheiro de base de dados 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Utilize uma conta de utilizador diferente não raiz - Para utilizar uma conta de utilizador diferente não raiz
  - Atualize o recipiente criar opções para especificar adicionar `*"User": "user_name | user_id*` o par de valores-chave sob opções de criação de recipientes. Substitua user_name ou user_id por um user_name ou user_id reais do seu anfitrião estivador. 
  - Altere as permissões no volume do diretório/montagem.

## <a name="persist-your-data"></a>Persistir os seus dados

As alterações de configuração do Azure SQL Edge e os ficheiros de base de dados são persistidos no recipiente, mesmo que reinicie o contentor com `docker stop` e `docker start` . No entanto, se retirar o recipiente com `docker rm` , tudo o que está no recipiente é apagado, incluindo a Borda Azure SQL e as suas bases de dados. A secção seguinte explica como utilizar **volumes de dados** para persistir nos ficheiros da base de dados, mesmo que os recipientes associados sejam eliminados.

> [!IMPORTANT]
> Para a Azure SQL Edge, é fundamental que compreenda a persistência de dados no Docker. Para além da discussão nesta secção, consulte a documentação do Docker sobre [como gerir dados em contentores do Docker.](https://docs.docker.com/engine/tutorials/dockervolumes/)

### <a name="mount-a-host-directory-as-data-volume"></a>Monte um diretório de anfitrião como volume de dados

A primeira opção é montar um diretório no seu anfitrião como um volume de dados no seu recipiente. Para isso, use o `docker run` comando com a `-v <host directory>:/var/opt/mssql` bandeira. Isto permite restaurar os dados entre execuções de contentores.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

Esta técnica também permite partilhar e ver os ficheiros do hospedeiro fora do Docker.

> [!IMPORTANT]
> O mapeamento de volume do anfitrião para **Docker no Windows** não suporta atualmente mapear o `/var/opt/mssql` diretório completo. No entanto, pode mapear uma subdiretória, como `/var/opt/mssql/data` a sua máquina hospedeira.

> [!IMPORTANT]
> O mapeamento de volume do anfitrião para **Docker em Mac** com a imagem Azure SQL Edge não é suportado neste momento. Em vez disso, utilize recipientes de volume de dados. Esta restrição é específica para o `/var/opt/mssql` diretório. Ler de um diretório montado funciona bem. Por exemplo, pode montar um diretório de anfitriões usando -v no Mac e restaurar uma cópia de segurança de um ficheiro .bak que reside no anfitrião.

### <a name="use-data-volume-containers"></a>Utilize recipientes de volume de dados

A segunda opção é utilizar um recipiente de volume de dados. Pode criar um recipiente de volume de dados especificando um nome de volume em vez de um diretório de anfitrião com o `-v` parâmetro. O exemplo a seguir cria um volume de dados partilhado chamado **sqlvolume**.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

> [!NOTE]
> Esta técnica para criar implicitamente um volume de dados no comando de execução não funciona com versões mais antigas do Docker. Nesse caso, utilize os passos explícitos descritos na documentação do Docker, [criando e montando um recipiente de volume de dados](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container).

Mesmo que pare e remova este recipiente, o volume de dados persiste. Pode vê-lo com o `docker volume ls` comando.

```bash
docker volume ls
```

Se, em seguida, criar outro recipiente com o mesmo nome de volume, o novo recipiente utiliza os mesmos dados Azure SQL Edge contidos no volume.

Para remover um recipiente de volume de dados, utilize o `docker volume rm` comando.

> [!WARNING]
> Se eliminar o recipiente de volume de dados, quaisquer dados do Azure SQL Edge no recipiente são *permanentemente* eliminados.


## <a name="next-steps"></a>Passos seguintes

- [Ligue-se à Borda Azure SQL](connect.md)
- [Construa uma solução IoT de ponta a ponta com o SQL no Edge](tutorial-deploy-azure-resources.md)
