---
title: Registar análises de origem e configuração SAP S/4HANA (pré-visualização) em Azure Purview
description: Este artigo descreve como registar a fonte SAP S/4HANA em Azure Purview e configurar uma varredura.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 6d31bd0911b5cf765215e6a482a39b2458c4ba0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696165"
---
# <a name="register-and-scan-a-sap-s4hana-source-preview"></a>Registar e digitalizar uma fonte SAP S/4HANA (pré-visualização)

Este artigo descreve como registar uma fonte SAP S/4HANA em Purview e configurar uma varredura.

## <a name="supported-capabilities"></a>Capacidades suportadas

A fonte SAP S/4HANA suporta a **varredura completa** para extrair metadados de uma instância SAP S/4HANA e buscar **a Linhagem** entre os ativos de dados.

## <a name="prerequisites"></a>Pré-requisitos

1.  Configurar o último [tempo de integração auto-hospedado.](https://www.microsoft.com/download/details.aspx?id=39717)
    Para obter mais informações, consulte [Criar e configurar um tempo de integração auto-organizado.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

2.  Certifique-se de que o [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado na sua máquina virtual onde está instalado o tempo de funcionação da integração auto-hospedada.

3.  Certifique-se de que \" o Visual C++ Redistributable Update 4 \" está instalado na máquina de tempo de execução de integração auto-hospedada. Se \' ainda não o tiver instalado, descarregue-o a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=30679)

4.  Descarregue o Conector SAP de 64 [bits para o Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) do site da SAP \' e instale-o na máquina de execução de integração auto-hospedada. Durante a instalação, certifique-se de que seleciona os **Conjuntos de Instalação para** a opção GAC na janela **de etapas de configuração opcionais.**

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="pré-requisito" border="true":::

5.  O conector lê metadados da SAP utilizando o Conector Java (JCo) 3.0 API. Por isso, certifique-se de que o Java Connector está disponível na sua máquina virtual onde o tempo de funcionação de integração auto-hospedado está instalado.
    Certifique-se de que está a utilizar a distribuição JCo correta para o seu ambiente. Por exemplo, numa máquina do Microsoft Windows, certifique-se de que os ficheiros sapjco3.jar e sapjco3.dll estão disponíveis.

    > [!Note] 
    >O condutor deve estar acessível a todas as contas do VM. Não o instale numa conta de utilizador.

6.  Implementar o módulo de função ABAP de extração de metadados no servidor SAP seguindo os passos mencionados no [guia de implementação de funções ABAP](abap-functions-deployment-guide.md). Necessitará de uma conta de programador ABAP para criar o módulo de função RFC no servidor SAP. A conta de utilizador requer permissões suficientes para ligar ao servidor SAP e executar os seguintes módulos de função RFC:
    -   STFC_CONNECTION (verificar conectividade)
    -   RFC_SYSTEM_INFO (verificar informações do sistema)

## <a name="setting-up-authentication-for-a-scan"></a>Configuração da autenticação para uma varredura

A única autenticação suportada para a fonte SAP S/4HANA é **a autenticação básica**

## <a name="register-sap-s4hana-source"></a>Registar fonte SAP S/4HANA

Para registar uma nova fonte SAP S/4HANA no seu catálogo de dados, faça o seguinte:

1.  Navegue para a sua conta Depview.
2.  Selecione **Fontes** na navegação à esquerda.
3.  Selecione **Registar-se**
4.  Nas fontes de registo, selecione **SAP S/4HANA.** Selecione **Continuar**

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana.png" alt-text="registar opções SAPS/4Hana" border="true":::

No ecrã **'Fontes de Registo' (SAP S/4HANA),** faça o seguinte:

1.  Introduza um **Nome** que a fonte de dados será listada no Catálogo.

2.  Introduza o nome do **servidor 'Aplicação'** para ligar à fonte SAP S/4HANA. Também pode ser um endereço IP do anfitrião do servidor de aplicações SAP.

3.  Introduza o **número do Sistema** SAP . Este é um número inteiro de dois dígitos entre 00 e 99.

4.  Selecione uma coleção ou crie uma nova (Opcional)

5.  Termine para registar a fonte de dados.

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana-2.png" alt-text="registar SAP S/4HANA" border="true":::

## <a name="creating-and-running-a-scan"></a>Criar e executar uma digitalização

Para criar e executar uma nova digitalização, faça o seguinte:

1.  No Centro de Gestão, clique nos tempos de integração. Certifique-se de que é configurado um tempo de integração auto-hospedado. Se não for configurado, use os passos [aqui](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) mencionados para criar um tempo de integração auto-hospedado

2.  Navegue para **fontes.**

3.  Selecione a fonte SAP S/4HANA registada.

4.  Selecione **+ Nova digitalização**

5.  Forneça os detalhes abaixo:

    a.  **Nome**: O nome da digitalização

    b.  **Conecte-se através do tempo de execução** da integração : Selecione o tempo de integração auto-organizado configurado.

    c.  **Credencial**: Selecione a credencial para ligar à sua fonte de dados. Certifique-se de:

    -   Selecione a autenticação básica enquanto cria uma credencial.
    -   Forneça um ID do utilizador para ligar ao servidor SAP no campo de entrada do nome do utilizador.
    -   Guarde a palavra-passe do utilizador utilizada para ligar ao servidor SAP na chave secreta.

    d.  **ID do cliente:** Insira aqui o ID do cliente do sistema SAP. O cliente é identificado com um número numérico de três dígitos de 000 a 999.

    e.  **Caminho da biblioteca JCo**: Especifique o caminho para a pasta onde estão localizadas as bibliotecas JCo.

    f.  **Memória máxima disponível:** Memória máxima (em GB) disponível no VM do cliente para ser usada através de processos de digitalização. Isto depende do tamanho da fonte SAP S/4HANA a ser digitalizada.

    :::image type="content" source="media/register-scan-saps4hana-source/scan-saps-4-hana.png" alt-text="digitalização SAP S/4HANA" border="true":::

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
