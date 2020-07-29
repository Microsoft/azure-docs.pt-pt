---
title: Configure Borda SQL Azure (Pré-visualização)
description: Saiba como configurar a borda Azure SQL (pré-visualização).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636245"
---
# <a name="configure-azure-sql-edge-preview"></a>Configure Borda SQL Azure (Pré-visualização)

Azure SQL Edge suporta a configuração através de uma das duas opções seguintes:

- Variáveis de ambiente
- Um ficheiro mssql.conf colocado na pasta /var/opt/mssql

> [!NOTE]
> A definição de variáveis ambientais substitui as definições especificadas no ficheiro mssql.conf.

## <a name="configure-by-using-environment-variables"></a>Configure usando variáveis ambientais

O Azure SQL Edge expõe várias variáveis ambientais diferentes que podem ser usadas para configurar o recipiente SQL Edge. Estas variáveis ambientais são um subconjunto dos disponíveis para o SQL Server em Linux. Para obter mais informações sobre o SQL Server sobre variáveis ambientais Linux, consulte [variáveis ambientais](/sql/linux/sql-server-linux-configure-environment-variables/).

O seguinte SQL Server na variável ambiente Linux não é suportado para Azure SQL Edge. Se definido, esta variável ambiente será ignorada durante a inicialização do recipiente.

| Variável de ambiente | Descrição |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Ativar o grupo de disponibilidade. Por exemplo, **1** está ativado e **0** está desativado. |

> [!IMPORTANT]
> A variável de ambiente **MSSQL_PID** para SQL Edge apenas aceita **Premium** e **Developer** como valores válidos. O Azure SQL Edge não suporta a inicialização utilizando uma chave de produto.

> [!NOTE]
> Descarregue os [Termos de Licença de Software da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128283) para Azure SQL Edge.

### <a name="specify-the-environment-variables"></a>Especificar as variáveis ambientais

Especifique variáveis ambientais para SQL Edge quando implementar o serviço através do [portal Azure](deploy-portal.md). Pode adicioná-las na secção **De Variáveis Ambientais** da implantação do módulo ou como parte das **Opções de Criação de Recipientes.**

Adicionar valores em **Variáveis Ambientais.**

![Definido usando a lista de variáveis ambientais](media/configure/set-environment-variables.png)

Adicione valores em **Opções de Criação de Recipientes.**

![Definir usando opções de criação de recipiente](media/configure/set-environment-variables-using-create-options.png)

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

As seguintes opções mssql.conf não são aplicáveis à SQL Edge:

|Opção|Descrição|
|:---|:---|
|**Feedback do cliente** | Escolha se o SQL Server envia feedback para a Microsoft. |
|**Perfil de correio de base de dados** | Desacrie o perfil de correio de base de dados predefinido para o SQL Server no Linux. |
|**Elevada disponibilidade** | Ativar grupos de disponibilidade. |
|**Coordenador de transações distribuídas da Microsoft** | Configure e resolva os problemas msdTC em Linux. As opções adicionais de configuração distribuídas relacionadas com transações não são suportadas para o SQL Edge. Para obter mais informações sobre estas opções de configuração adicionais, consulte [Configure MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**MLServices EULAs** | Aceite eulas R e Python para pacotes de aprendizagem automática Azure. Aplica-se apenas ao SQL Server 2019.|
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

## <a name="next-steps"></a>Próximos passos

- [Ligue-se à Borda Azure SQL](connect.md)
- [Construa uma solução IoT de ponta a ponta com o SQL no Edge](tutorial-deploy-azure-resources.md)
