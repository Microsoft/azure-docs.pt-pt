---
title: Registar a origem da Oracle e configurar as análises (pré-visualização) em Azure Purview
description: Este artigo descreve como registar a fonte da Oracle em Azure Purview e configurar uma varredura.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 76aadd667691e12c61e0e5e13c13ca0241a9f0ce
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045506"
---
# <a name="register-and-scan-oracle-source-preview"></a>Registar e scan o oráculo (pré-visualização)

Este artigo descreve como registar uma base de dados da Oracle em Purview e configurar uma varredura.

## <a name="supported-capabilities"></a>Capacidades suportadas

A fonte da Oracle suporta a **verificação completa** para extrair metadados de uma base de dados da Oracle e buscar **a Linhagem** entre os ativos de dados.

## <a name="prerequisites"></a>Pré-requisitos

1.  Configurar o último [tempo de integração auto-hospedado.](https://www.microsoft.com/download/details.aspx?id=39717)
    Para obter mais informações, consulte [Criar e configurar um tempo de integração auto-organizado.](../data-factory/create-self-hosted-integration-runtime.md)

2.  Certifique-se de que [o JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado na sua máquina virtual onde está instalado o tempo de funcionação da integração auto-hospedada.

3.  Certifique-se de que \" o Visual C++ Redistributable Update 4 \" está instalado na máquina de tempo de execução de integração auto-hospedada. Se \' ainda não o tiver instalado, descarregue-o a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=30679)

4.  Terá de descarregar manualmente um controlador Oracle JDBC a partir [daqui](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) na sua máquina virtual, onde o tempo de funcionamento da integração auto-hospedada está a decorrer.

    > [!Note] 
    > O condutor deve estar acessível a todas as contas do VM. Não o instale numa conta de utilizador.

5.  As versões suportadas da base de dados da Oracle são de 6i a 19c.

6.  Permissão do utilizador: Para garantir uma verificação bem sucedida pela primeira vez, é necessária uma permissão do tipo Full Sys Admin.

    Para análises posteriores, é necessário um acesso apenas de leitura às tabelas do sistema. O utilizador deve ter permissão para criar uma sessão, bem como função SELECT \_ CATALOG \_ ROLE atribuído. Em alternativa, o utilizador pode ter a permissão SELECT concedida para cada tabela de sistema individual que este conector consulta metadados a partir de:
       > grant criar sessão ao \[ \] utilizador;\
        conceder o seleto de todos os \_ utilizadores ao \[ \] utilizador;\
        conceder seleção em objetos dba \_ ao \[ \] utilizador;\
        conceder o seleto sobre comentários do separador DBA \_ \_ ao utilizador \[ \] ;\
        conceder seleção em \_ \_ localizações externas dba ao \[ utilizador \] ;\
        conceder selecione em diretórios dba \_ para \[ \] utilizador;\
        conceder seleção em dba \_ mviews ao \[ utilizador \] ;\
        conceder o seleto em colunas dba \_ clu \_ ao \[ \] utilizador;\
        conceder o seleto nas colunas do separador DBA \_ \_ ao utilizador \[ \] ;\
        conceder selecione sobre comentários dba \_ col \_ ao utilizador \[ \] ;\
        conceder o seleto sobre as restrições dba \_ ao \[ \] utilizador;\
        conceder o seleto nas colunas dba \_ cons \_ ao \[ \] utilizador;\
        conceder seleção em índices dba \_ ao \[ \] utilizador;\
        conceder o seleto \_ em colunas dba \_ ind ao \[ \] utilizador;\
        conceder o seleto sobre os procedimentos dba \_ ao \[ \] utilizador;\
        conceder selecione sobre os sinónimos dba \_ ao \[ \] utilizador;\
        conceder seleção sobre vistas dba \_ para \[ o utilizador \] ;\
        conceder seleção na fonte dba \_ ao \[ \] utilizador;\
        conceder selecione em \_ dba triggers para \[ o utilizador \] ;\
        conceder seleção sobre argumentos dba \_ ao \[ \] utilizador;\
        conceder selecione em sequências dba \_ para \[ o utilizador \] ;\
        conceder seleção sobre dependências dba \_ ao \[ \] utilizador;\
        conceder o seleto em V \_ \$ INSTANCE ao \[ \] utilizador;\
        conceder a seleção na base de dados V \_ \$ ao \[ \] utilizador;
    
## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

A única autenticação suportada para uma fonte Oráculo é **a autenticação básica.**

## <a name="register-an-oracle-source"></a>Registar uma fonte oráculo

Para registar uma nova fonte da Oracle no seu catálogo de dados, faça o seguinte:

1.  Navegue para a sua conta Depview.
2.  Selecione **Fontes** na navegação à esquerda.
3.  Selecione **Registar-se**
4.  Nas fontes de registo, selecione **Oráculo**. Selecione **Continuar**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="registar fontes" border="true":::

No ecrã **'Oráculo' das fontes de registo,** faça o seguinte:

1.  Introduza um **Nome** que a fonte de dados será listada no Catálogo.

2.  Insira o nome **hospedeiro** para ligar a uma fonte oráculo. Isto pode ser:
    - Um nome de anfitrião usado pelo JDBC para ligar ao servidor de base de dados. Por exemplo, MyDatabaseServer.com ou
    - Endereço IP. Por exemplo, 192.169.1.2 ou
    - A sua cadeia de ligação JDBC totalmente qualificada. Por exemplo, \\
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD \_ BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT \_ DATA=(SERVICE \_ NAME=orcl))

3.  Introduza o **número de porta** utilizado pelo JDBC para ligar ao servidor de base de dados (1521 por defeito para o Oracle).

4.  Introduza o **nome de serviço Oracle** utilizado pela JDBC para ligar ao servidor de base de dados.

5.  Selecione uma coleção ou crie uma nova (Opcional)

6.  Termine para registar a fonte de dados.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="registar opções de fontes" border="true":::

## <a name="creating-and-running-a-scan"></a>Criar e executar uma digitalização

Para criar e executar uma nova digitalização, faça o seguinte:

1.  No Centro de Gestão, clique nos tempos de integração. Certifique-se de que é configurado um tempo de integração auto-hospedado. Se não for configurado, utilize os passos [aqui](./manage-integration-runtimes.md) mencionados para criar um tempo de integração auto-hospedado.

2.  Navegar para **fontes**.

3.  Selecione a fonte oráculo registada.

4.  Selecione **+ Nova digitalização.**

5.  Forneça os detalhes abaixo:

    a.  **Nome**: O nome da digitalização

    b.  **Conecte-se através do tempo de execução** da integração : Selecione o tempo de integração auto-organizado configurado

    c.  **Credencial**: Selecione a credencial para ligar à sua fonte de dados. Certifique-se de:
    - Selecione a autenticação básica enquanto cria uma credencial.        
    - Forneça o nome de utilizador utilizado pelo JDBC para ligar ao servidor de base de dados no campo de entrada do nome de utilizador        
    - Guarde a palavra-passe do utilizador utilizada pelo JDBC para ligar ao servidor de base de dados na chave secreta.

    d.  **Esquema**: Lista subconjunto de esquemas a importar expressos em lista separada de pontos de e vírgula. Por exemplo, esquema1; schema2. Todos os esquemas de utilizador são importados se a lista estiver vazia. Todos os esquemas do sistema (por exemplo, SysAdmin) e objetos são ignorados por padrão. Quando a lista está vazia, todos os esquemas disponíveis são importados.
        Padrões aceitáveis de nome de esquema usando sintaxe de expressões SQL LIKE incluem a utilização de %, por exemplo. A%; %B; %C%; D
       -   começar com A ou        
       -   terminar com B ou        
       -   conter C ou        
       -   igual D

    O uso de NÃO e caracteres especiais não é aceitável.

6.  **Localização do condutor**: Especifique o caminho para a localização do controlador JDBC no seu VM onde o tempo de funcionamento da integração do auto-anfitrião está a decorrer. Este deve ser o caminho para a localização válida da pasta JAR.

7.  **Memória máxima disponível**: Memória máxima (em GB) disponível no VM do cliente para ser utilizada através de processos de digitalização. Isto depende do tamanho da fonte SAP S/4HANA a ser digitalizada.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="oráculo de escanda" border="true":::

8.  Clique em **Continuar**.

9.  Escolha o **seu gatilho de digitalização**. Pode configurar um horário ou fazer a tomografia uma vez.

10.  Reveja a sua digitalização e clique em **Guardar e Executar.**

## <a name="viewing-your-scans-and-scan-runs"></a>Visualizar os seus exames e digitalizar corre

1. Navegue para o centro de gestão. Selecione **fontes de dados** sob as fontes e a secção **de digitalização.**

2. Selecione a fonte de dados desejada. Verá uma lista de digitalizações existentes nessa fonte de dados.

3. Selecione a verificação cujos resultados está interessado em visualizar.

4. Esta página irá mostrar-lhe todas as verificações anteriores, juntamente com métricas e estado para cada execução de digitalização. Também irá mostrar se a sua digitalização foi programada ou manual, quantos ativos tinham classificações aplicadas, quantos ativos totais foram descobertos, o início e o fim da verificação, e a duração total da verificação.

## <a name="manage-your-scans"></a>Gerencie os seus exames

Para gerir ou apagar uma verificação, faça o seguinte:

1. Navegue para o centro de gestão. Selecione **fontes de dados** sob as **Fontes e a** secção de digitalização e, em seguida, selecione na fonte de dados desejada.

2. Selecione a digitalização que gostaria de fazer. Pode editar a digitalização selecionando **Editar.**

3. Pode eliminar a sua verificação selecionando **Delete**.

## <a name="next-steps"></a>Passos seguintes

- [Navegue no catálogo de dados Azure Purview Data](how-to-browse-catalog.md)
- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)