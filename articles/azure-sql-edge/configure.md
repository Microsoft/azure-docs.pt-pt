---
title: Configure Azure SQL Edge (Pré-visualização)
description: Saiba configurar o Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5dcdd1604674ca56fb7a646d4c571d63bd2c0e3e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596961"
---
# <a name="configure-azure-sql-edge-preview"></a>Configure Azure SQL Edge (Pré-visualização)

O Azure SQL Edge suporta a configuração através de uma das duas opções seguintes:

- Utilização de Variáveis Ambientais.
- Utilizando o ficheiro mssql.conf colocado na pasta /var/opt/mssql.

> [!NOTE]
> Definição De Ambiente Variáveis sobrepõe-se às definições especificadas no ficheiro mssql.conf.

## <a name="configure-using-environment-variables"></a>Configurar usando variáveis ambientais

O Azure SQL Edge expõe várias variáveis ambientais diferentes que podem ser usadas para configurar o recipiente SQL Edge. Estas variáveis ambientais são um subconjunto das variáveis ambientais disponíveis para o SQL Server em Linux. Para obter mais informações sobre o SQL Server sobre variáveis ambientais Linux, consulte [Variáveis ambientais](/sql/linux/sql-server-linux-configure-environment-variables/).

O seguinte Servidor SQL nas variáveis ambientais Linux não são suportados para Azure SQL Edge. Se definidos, estas variáveis ambientais serão ignoradas durante a inicialização do recipiente.

| Variável de ambiente | Descrição |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Ativar o Grupo de Disponibilidade. Por exemplo, o '1' está ativado e o '0' está desativado |

> [!IMPORTANT]
> A variável *ambiente MSSQL_PID* para o SQL Edge só aceita **Premium** e **Developer** como os valores válidos. O Azure SQL Edge não suporta a inicialização utilizando uma chave do produto.

> [!NOTE]
> Para descarregar o contrato de licença de utilizador final Azure SQL Edge, consulte o [Contrato de Licenciamento de Utilizador Final](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>Especificação das Variáveis Ambientais

As variáveis ambientais para o SQL Edge podem ser especificadas ao implantar o Azure SQL Edge através do [portal Azure](deploy-portal.md). Isto pode ser adicionado quer na secção "Variáveis Ambientais" da implantação do módulo, quer como parte do recipiente, cria ndo a opção como descrito abaixo.

*Definir usando opções de variáveis ambientais*

![definido usando a lista de variáveis ambientais](media/configure/set-environment-variables.png)

*Definir usando o recipiente criar opções*

![conjunto usando o recipiente criar opções](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Configure usando o ficheiro mssql.conf

O Azure SQL Edge não inclui o utilitário de [configuração mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) como o SQL Server no Linux, como o ficheiro mssql.conf precisa de ser configurado manualmente e colocado na unidade de armazenamento persistente que é mapeada para a pasta /var/opt/mssql/ no módulo SQL Edge. Ao implantar o SQL Edge do Mercado Azure, este mapeamento é especificado como a opção **Mounts" no recipiente criar opção de criação de opção

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

As seguintes opções mssql.conf não são aplicáveis ao SQL Edge:
</br></br>
|Opção|Descrição|
|:---|:---|
|**Feedback do cliente** | Escolha se o SQL Server envia ou não feedback para a Microsoft. |
|**Perfil de correio de base de dados** | Detete o perfil de correio de base de dados predefinido para o Servidor SQL no Linux. |
|**Alta disponibilidade** | Ativar grupos de disponibilidade. |
|**Coordenador de Transações Distribuídas da Microsoft** | Configure e problemas mSDTC em Linux. As opções adicionais de configuração relacionadas com transações distribuídas também não são suportadas para o SQL Edge. Para obter mais informações sobre estas opções de configuração adicionais, consulte [Configure MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) |
|**MLServices EULAs** | Aceite os EULAs R e Python para pacotes de serviços de aprendizagem automática. Aplica-se apenas ao SQL Server 2019.|
|**acesso à rede de saída** |Permitir o acesso à rede de saída para as extensões R, Python e Java dos Serviços de [Aprendizagem automática.](/sql/linux/sql-server-linux-setup-machine-learning/)|

Um ficheiro mssql.conf de amostra, que funciona para o SQL Edge, é fornecido abaixo. Para obter mais informações sobre o formato para ficheiro mssql.conf, consulte o [formato mssql.conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

- [Ligue-se a Azure SQL Edge](connect.md)
- [Construção de uma solução IoT de ponta a ponta com SQL Edge](tutorial-deploy-azure-resources.md)
