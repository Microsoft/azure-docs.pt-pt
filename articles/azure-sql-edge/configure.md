---
title: Configure Borda SQL Azure (Pré-visualização)
description: Saiba como configurar a borda Azure SQL (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dc2b76a31982a3f72da02348c1a4796212887cb7
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84168255"
---
# <a name="configure-azure-sql-edge-preview"></a>Configure Borda SQL Azure (Pré-visualização)

Azure SQL Edge suporta a configuração através de uma das duas opções seguintes:

- Usando variáveis ambientais.
- Utilização do ficheiro mssql.conf colocado na pasta /var/opt/mssql.

> [!NOTE]
> Definição De Variáveis de Ambiente sobrepõe-se às definições especificadas no ficheiro mssql.conf.

## <a name="configure-using-environment-variables"></a>Configurar usando variáveis ambientais

O Azure SQL Edge expõe várias variáveis ambientais diferentes que podem ser usadas para configurar o recipiente SQL Edge. Estas variáveis ambientais são um subconjunto das variáveis ambientais disponíveis para o SQL Server em Linux. Para obter mais informações sobre o SQL Server sobre variáveis ambientais Linux, consulte [Variáveis ambientais](/sql/linux/sql-server-linux-configure-environment-variables/).

As seguintes variáveis de ambiente SQL em Linux NÃO são suportadas para Azure SQL Edge. Se definidos, estas variáveis ambientais serão ignoradas durante a inicialização do contentor.

| Variável de ambiente | Descrição |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Ativar o Grupo de Disponibilidade. Por exemplo, '1' está ativado e '0' é desativado |

> [!IMPORTANT]
> A variável de ambiente *MSSQL_PID* para SQL Edge apenas aceita **Premium** e **Developer** como valores válidos. O Azure SQL Edge não suporta a inicialização utilizando uma chave de produto.

> [!NOTE]
> Para descarregar o contrato de licença de utilizador final Azure SQL Edge, consulte [o Contrato de Licenciamento do Utilizador Final](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>Especificar as Variáveis Ambientais

As variáveis ambientais para sql Edge podem ser especificadas ao implementar a Borda Azure SQL através do [portal Azure](deploy-portal.md). Isto pode ser adicionado na secção "Variáveis ambientais" da implantação do módulo ou como parte da opção de criação do recipiente, conforme descrito abaixo.

*Definir usando opções de variáveis ambientais*

![definir usando lista de variáveis ambientais](media/configure/set-environment-variables.png)

*Definir usando opções de criação de recipiente*

![definir usando opções de criação de recipiente](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Configure usando o ficheiro mssql.conf

O Azure SQL Edge não inclui o [utilitário de configuração mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) como o SQL Server no Linux, pois tal o ficheiro mssql.conf precisa de ser configurado manualmente e colocado na unidade de armazenamento persistente que é mapeada para a pasta /var/opt/mssql/ no módulo SQL Edge. Ao implementar a SqL Edge a partir do Mercado Azure, este mapeamento é especificado como a opção **Mounts" na opção de criação de recipientes

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

As seguintes opções mssql.conf não são aplicáveis à SqL Edge:

|Opção|Descrição|
|:---|:---|
|**Feedback do cliente** | Escolha se o SQL Server envia ou não feedback para a Microsoft. |
|**Perfil de correio de base de dados** | Desacrie o perfil de correio de base de dados predefinido para o SQL Server no Linux. |
|**Elevada disponibilidade** | Ativar grupos de disponibilidade. |
|**Coordenador de transações distribuídas da Microsoft** | Configure e resolva os problemas msdTC em Linux. As opções adicionais de configuração distribuídas relacionadas com transações também não são suportadas para o SQL Edge. Para obter mais informações sobre estas opções de configuração adicionais, consulte [Configure MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**MLServices EULAs** | Aceite pacotes de R e Python EULAs para serviços de aprendizagem automática. Aplica-se apenas ao SQL Server 2019.|
|**saída da rede de trabalho** |Permitir o acesso à rede de saída para [extensões de Serviços de Machine Learning](/sql/linux/sql-server-linux-setup-machine-learning/) R, Python e Java.|

Um ficheiro mssql.conf de amostra, que funciona para SQL Edge é fornecido abaixo. Para obter mais informações sobre o formato do ficheiro mssql.conf, consulte [o formato mssql.conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

## <a name="next-step"></a>Passo seguinte

- [Ligue-se à Borda Azure SQL](connect.md)
- [Construção de uma solução IoT de ponta a ponta com SQL Edge](tutorial-deploy-azure-resources.md)
