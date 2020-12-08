---
title: Registar uma fonte teradata e digitalizações de configuração (pré-visualização)
description: Este artigo descreve como registar uma fonte teradata em Azure Purview e configurar uma digitalização.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841953"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registar e digitalizar fonte teradata (pré-visualização)

Este artigo descreve como registar uma fonte teradata em Purview e configurar uma varredura.

> [!IMPORTANT]
> Esta fonte de dados está atualmente em pré-visualização. Pode experimentar e dar-nos feedback.

## <a name="supported-capabilities"></a>Capacidades suportadas

A fonte teradata suporta **a varredura completa** para extrair metadados de uma base de dados Teradata e buscar a **Linhagem** entre os ativos de dados.

## <a name="prerequisites"></a>Pré-requisitos

- O conector suporta a loja de dados localizada apenas dentro de uma rede no local, uma rede virtual Azure ou Amazon Virtual Private Cloud. Por isso, é necessário configurar um [tempo de integração auto-hospedado](manage-integration-runtimes.md) para se ligar a ele.

- Certifique-se de que o Ambiente de Execução Java (JRE) está instalado na sua máquina virtual onde está instalado o tempo de funcionação da integração auto-hospedada.
 
- Certifique-se de que "Visual C++ Redistributable Update 4" está instalado na máquina de execução de integração auto-hospedada. Se ainda não o tiver instalado, descarregue-o a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=30679)

- Terá de instalar manualmente um controlador chamado Teradata JDBC Driver na sua máquina virtual no local. O ficheiro JAR executável pode ser descarregado a partir do website da [Teradata.](https://downloads.teradata.com/)

    > [!Note] 
    > O condutor deve estar acessível a todas as contas do VM. Por favor, não instale numa conta de utilizador.

- As versões de base de dados teradata suportadas são **de 12.x a 16.x**. Certifique-se de que o acesso à fonte teradata está a ser digitalizado.

### <a name="feature-flag"></a>Bandeira de recurso

O registo e digitalização de uma fonte teradata está disponível atrás de uma bandeira de recurso. Apendam o seguinte ao seu URL: *?feature.ext.datasource=%7b"teradata":"verdadeiro"%7d* 

Por exemplo, URL completo [ https://web.purview.azure.com/?feature.ext.datasource=%7b "metadados":"verdadeiros"%7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura
A única forma suportada de configurar a autenticação para uma fonte teradata é a autenticação **de bases de dados básicas**

## <a name="register-a-teradata-source"></a>Registar uma fonte teradata

Para registar uma nova fonte Teradata no seu catálogo de dados, faça o seguinte:

1. Navegue para a sua conta Desemis
2. Selecione **Fontes** na navegação à esquerda
3. Selecione **Registar-se**
4. Nas **fontes de registo**, selecione **Teradata**
5. Selecione **Continuar**

No ecrã **'Registar fontes' (Teradata),** faça o seguinte:

1. Introduza um **Nome** que a fonte de dados será listada no Catálogo.
2. Insira o nome **anfitrião** para ligar a uma fonte teradata. Também pode ser um endereço IP ou um fio de ligação totalmente qualificado para o servidor.
3. Selecione uma coleção ou crie uma nova (Opcional)
4. **Termine** para registar a fonte de dados.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="registar opções de fontes" border="true":::

## <a name="creating-and-running-a-scan"></a>Criar e executar uma digitalização

Para criar e executar uma nova digitalização, faça o seguinte:
1. No Centro de Gestão, clique nos *tempos de integração.* Certifique-se de que é configurado um tempo de integração auto-hospedado. Se não for configurado, utilize os passos [aqui](manage-integration-runtimes.md) mencionados para criar um tempo de integração auto-hospedado para digitalizar em um local ou VM Azure que tenha acesso à sua rede no local.

2. Em seguida, navegue **fontes**

3. Selecione a fonte teradata registada.

4. Selecione + Nova digitalização
 
5. Forneça os detalhes abaixo:

    - Nome: O nome da digitalização

    - Conecte-se através do tempo de execução da integração: Selecione o tempo de execução de integração auto-organizado configurado

    - Método de autenticação: A autenticação da base de dados é a única opção suportada por enquanto. Isto será selecionado por padrão

    - Nome do utilizador: Um nome de utilizador para ligar ao servidor de bases de dados. Este nome de utilizador deveria ter lido acesso ao servidor

    - Palavra-passe: A palavra-passe do utilizador utilizada para ligar ao servidor de bases de dados

    - Esquema: Lista subconjunto de esquemas para importar expressos como uma lista separada de pontos e vírgulas. por exemplo, esquema1; schema2. Todos os esquemas de utilizador são importados se a lista estiver vazia. Todos os esquemas do sistema (por exemplo, SysAdmin) e objetos são ignorados por padrão.

        Padrões aceitáveis de nomes de esquemas usando sintaxe de expressões SQL LIKE incluem a utilização , por exemplo, A%; %B; %C%; D
        - começar com A ou    
        - terminar com B ou    
        - conter C ou    
        - igual D

        O uso de NÃO e caracteres especiais não são aceitáveis

    - Localização do condutor: Caminho completo para a localização do controlador Teradata na VM do cliente. O nome do condutor teradata JDBC deve ser: com.teradata.jdbc.TeraDriver

    - Memória máxima disponível: Memória máxima (em GB) disponível no VM do cliente para ser usada através de processos de digitalização. Isto depende do tamanho da fonte Teradata para ser digitalizada.

    > [!Note] 
    > Como regra do polegar, por favor forneça memória de 2GB para cada 1000 mesas

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="digitalização de configuração" border="true":::

6. Clique em *Continuar.*

7. Escolha o seu gatilho de digitalização. Pode configurar um horário ou fazer a tomografia uma vez.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="gatilho de digitalização" border="true":::

8. Reveja a sua digitalização e clique em *Save and Run.*

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
