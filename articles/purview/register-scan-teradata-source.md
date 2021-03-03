---
title: Registar uma fonte teradata e configurar digitalizações (pré-visualização) em Azure Purview
description: Este artigo descreve como registar uma fonte teradata em Azure Purview e configurar uma digitalização.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 0885870497ef3488d78fe899c96ee2a82a5b84fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696189"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registar e digitalizar fonte teradata (pré-visualização)

Este artigo descreve como registar uma fonte teradata em Purview e configurar uma varredura.

## <a name="supported-capabilities"></a>Capacidades suportadas

A fonte teradata suporta **a varredura completa** para extrair metadados de uma base de dados Teradata e buscar a **Linhagem** entre os ativos de dados.

## <a name="prerequisites"></a>Pré-requisitos

1.  Configurar o último [tempo de integração auto-hospedado.](https://www.microsoft.com/download/details.aspx?id=39717)
    Para obter mais informações, consulte [Criar e configurar um tempo de integração auto-organizado.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

2.  Certifique-se de que o [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado na sua máquina virtual onde está instalado o tempo de funcionação da integração auto-hospedada.

3.  Certifique-se de que \" o Visual C++ Redistributable Update 4 \" está instalado na máquina de tempo de execução de integração auto-hospedada. Se \' ainda não o tiver instalado, descarregue-o a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=30679)

4.  Terá de descarregar manualmente o JDBC Driver da Teradata na sua máquina virtual onde o tempo de funcionamento da integração auto-hospedada está a decorrer.
    O ficheiro JAR executável pode ser descarregado a partir do [website](https://downloads.teradata.com/)da Teradata.

    > [!Note]
    > O condutor deve estar acessível a todas as contas do VM. Por favor, não instale numa conta de utilizador.

5.  As versões de base de dados teradata suportadas são de 12.x a 16.x. Certifique-se de que o acesso à fonte teradata está a ser digitalizado.

## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

A única autenticação suportada para uma fonte teradata é **a autenticação básica.**

## <a name="register-a-teradata-source"></a>Registar uma fonte teradata

Para registar uma nova fonte Teradata no seu catálogo de dados, faça o seguinte:

1.  Navegue para a sua conta Depview.
2.  Selecione **Fontes** na navegação à esquerda.
3.  Selecione **Registar-se**
4.  Nas fontes de registo, selecione **Teradata**. Selecione **Continuar**

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="registar opções Teradata" border="true":::

No ecrã **'Registar fontes' (Teradata),** faça o seguinte:

1.  Introduza um **Nome** que a fonte de dados será listada no Catálogo.

2.  Insira o nome **anfitrião** para ligar a uma fonte teradata. Também pode ser um endereço IP ou um fio de ligação totalmente qualificado para o servidor.

3.  Selecione uma coleção ou crie uma nova (Opcional)

4.  Termine para registar a fonte de dados.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="registar Teradata" border="true":::

## <a name="creating-and-running-a-scan"></a>Criar e executar uma digitalização

Para criar e executar uma nova digitalização, faça o seguinte:

1.  No Centro de Gestão, clique nos **tempos de integração.** Certifique-se de que é configurado um tempo de integração auto-hospedado. Se não for configurado, use os passos [aqui](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) mencionados para configurar um tempo de integração auto-hospedado

2.  Navegar para as **Fontes**

3.  Selecione a fonte teradata registada.

4.  Selecione **+ Nova digitalização**

5.  Forneça os detalhes abaixo:

    a.  **Nome**: O nome da digitalização

    b.  **Conecte-se através do tempo de execução** da integração : Selecione o tempo de integração auto-organizado configurado.

    c.  **Credencial**: Selecione a credencial para ligar à sua fonte de dados. Certifique-se de:

    -   Selecione a autenticação básica enquanto cria uma credencial.
    -   Forneça um nome de utilizador para ligar ao servidor de base de dados no campo de entrada do nome do utilizador
    -   Guarde a palavra-passe do servidor de base de dados na chave secreta.

        Para entender mais sobre credenciais, consulte o link [aqui](https://docs.microsoft.com/azure/purview/manage-credentials)

6.  **Esquema**: Lista subconjunto de esquemas a importar expressos em lista separada de pontos de e vírgula. por exemplo, esquema1; schema2. Todos os esquemas de utilizador são importados se a lista estiver vazia. Todos os esquemas do sistema (por exemplo, SysAdmin) e objetos são ignorados por padrão. Quando a lista está vazia, todos os esquemas disponíveis são importados.

        Acceptable schema name patterns using SQL LIKE expressions syntax include using %, e.g. A%; %B; %C%; D
        - start with A or    
        - end with B or    
        - contain C or    
        - equal D

        Usage of NOT and special characters are not acceptable

7.  **Localização do condutor**: Especifique o caminho para a localização do controlador JDBC no seu VM onde o tempo de funcionamento da integração do auto-anfitrião está a decorrer. Este deve ser o caminho para a localização válida da pasta JAR.

8.  **Memória máxima disponível:** Memória máxima (em GB) disponível no VM do cliente para ser usada através de processos de digitalização. Isto depende do tamanho da fonte Teradata para ser digitalizada.

    > [!Note] 
    > Como regra do polegar, por favor forneça memória de 2GB para cada 1000 mesas

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="digitalização de configuração" border="true":::

6.  Clique em **Continuar**.

7.  Escolha o **seu gatilho de digitalização**. Pode configurar um horário ou fazer a tomografia uma vez.

8.  Reveja a sua digitalização e clique em **Guardar e Executar.**

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