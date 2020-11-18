---
title: Traga o seu próprio ML para Azure Sentinel ! Microsoft Docs
description: Este artigo explica como criar e usar os seus próprios algoritmos de aprendizagem automática para análise de dados em Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 6a891a301d5869603a7d90d28bb9063d7d5bdb1d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660581"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Traga a sua própria Machine Learning (ML) para o Azure Sentinel

Machine Learning (ML) é um dos principais fundamentos do Azure Sentinel, e um dos principais atributos que a distinguem. O Azure Sentinel oferece ML em várias experiências: incorporado no motor de correlação [Fusion](fusion.md) e cadernos Jupyter, e na plataforma de ML Build-Your-Own (BYO ML) recentemente disponível. 

Os modelos de deteção de ML podem adaptar-se a ambientes individuais e a alterações no comportamento do utilizador, reduzir falsos positivos e identificar ameaças que não seriam encontradas com uma abordagem tradicional. Muitas organizações de segurança entendem o valor da ML para a segurança, embora poucas delas tenham o luxo de profissionais que possuam experiências tanto em segurança como em ML. Desenhamos o quadro aqui apresentado para que organizações de segurança e profissionais cresçam connosco na sua jornada ML. Organizações novas para ml, ou sem a experiência necessária, podem obter um valor de proteção significativo a partir das capacidades ML incorporadas de Azure Sentinel.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="quadro de aprendizagem automática":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>O que é a plataforma Bring Your Own Machine Learning (BYO-ML)

Para organizações que possuem recursos ML e gostariam de construir modelos ML personalizados para as suas necessidades únicas de negócio, oferecemos a **plataforma BYO-ML.** A plataforma faz [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)uso do ambiente / [Apache Spark](http://spark.apache.org/) e dos Cadernos Jupyter para produzir o ambiente ML. Fornece os seguintes componentes:

- um pacote BYO-ML, que inclui bibliotecas para ajudá-lo a aceder aos dados e empurrar os resultados de volta para Log Analytics (LA), para que possa integrar os resultados com a sua deteção, investigação e caça. 

- Modelos de algoritmo mL para você personalizar para se ajustar a problemas de segurança específicos na sua organização. 

- amostra de cadernos para treinar o modelo e agendar a pontuação do modelo. 

Além de tudo isto, pode trazer os seus próprios modelos ML, e/ou o seu próprio ambiente Spark, para se integrar com o Azure Sentinel.

Com a plataforma BYO-ML, pode começar a construir os seus próprios modelos ML: 

- O caderno com dados de amostra ajuda-o a obter experiência prática de ponta a ponta, sem se preocupar em lidar com os dados de produção.

- O pacote integrado ao ambiente Spark reduz os desafios e atritos na gestão da infraestrutura.

- As bibliotecas suportam movimentos de dados. Os cadernos de treino e pontuação demonstram a experiência de ponta a ponta e servem de modelo para se adaptar ao seu ambiente.

### <a name="use-cases"></a>Casos de utilização
 
A plataforma e o pacote BYO-ML reduzem significativamente o tempo e esforço necessários para construir as suas próprias deteções de ML, e eles libertam a capacidade de resolver problemas de segurança específicos em Azure Sentinel. A plataforma suporta os seguintes casos de utilização:

**Treine um algoritmo ML para obter um modelo personalizado:** Pode pegar num algoritmo ML existente (partilhado pela Microsoft ou pela comunidade de utilizadores) e treiná-lo facilmente nos seus próprios dados para obter um modelo ML personalizado que se adapte melhor aos seus dados e ambiente.

**Modifique um modelo de algoritmo ML para obter um modelo personalizado:** Pode modificar um modelo de algoritmo ML (partilhado pela Microsoft ou pela comunidade de utilizadores) e treinar o algoritmo modificado nos seus próprios dados, para obter um modelo personalizado para se adaptar ao seu problema específico.

**Crie o seu próprio modelo:** Crie o seu próprio modelo de raiz utilizando a plataforma BYO-ML da Azure Sentinel e os utilitários.

**Integre os seus Databricks/Ambiente de Faíscas:** Integre o ambiente databricks/faísca existente no Azure Sentinel e utilize bibliotecas e modelos BYO-ML para construir modelos ML para as suas situações únicas.

**Importe o seu próprio modelo ML:** Pode importar os seus próprios modelos ML e utilizar a plataforma e utilitários BYO-ML para integrá-los com o Azure Sentinel.

**Partilhe um algoritmo ML:** Partilhe um algoritmo ML para a comunidade adotar e adaptar-se.

**Use ml para capacitar secOps:** use o seu próprio modelo ML personalizado e resultados para caça, deteção, investigação e resposta.

Este artigo mostra-lhe os componentes da plataforma BYO-ML e como alavancar a plataforma e o algoritmo de Acesso a Recursos Anómalos para fornecer uma deteção de ML personalizada com o Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark Environment

[Apache Spark™](http://spark.apache.org/) deu um salto em frente na simplificação dos big data, fornecendo um quadro unificado para a construção de oleodutos de dados. A Azure Databricks leva isto mais longe, fornecendo uma plataforma cloud de gestão zero construída em torno de Spark. Recomendamos que utilize databricks para a sua plataforma BYO-ML, para que possa concentrar-se em encontrar respostas que produzam impacto imediato no seu negócio, em vez de abordar os problemas dos pipelines de dados e da plataforma.
Se já tiver Databricks ou qualquer outro ambiente de Faísca, e preferir utilizar a configuração existente, o pacote BYO-ML também funcionará bem neles. 

## <a name="byo-ml-package"></a>Pacote BYO-ML

O pacote BYO ML inclui as melhores práticas e pesquisa da Microsoft na parte frontal do ML para segurança. Neste pacote, fornecemos a seguinte lista de utilitários, cadernos e modelos de algoritmos para problemas de segurança.

| Nome de ficheiro | Descrição |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Contém utilitários para ler bolhas de Azure e escrever para Log Analytics. |
| AnomalousRASampleData | O Notebook demonstra a utilização do modelo de Acesso a Recursos Anómalos no Sentinel com dados de amostras de formação e teste gerados. |
| AnómaloRATraining.ipynb | Caderno para treinar o algoritmo, construir e salvar os modelos. |
| AnómaloRAScoring.ipynb | Notebook para agendar o modelo para executar, visualizar o resultado e escrever pontuação de volta para Azure Sentinel. |
|

O primeiro modelo de algoritmo ML que oferecemos é para [deteção de acesso a recursos anómalos.](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML) Baseia-se num algoritmo de filtragem colaborativo e é treinado com registos de acesso a partilha de ficheiros do Windows (Eventos de Segurança com ID do Evento 5140). A informação chave que precisa para este modelo no registo é o emparelhamento de utilizadores e recursos acedidos. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Exemplo Walkthrough: Deteção de acesso a partilha de ficheiros anómalos 

Agora que conhece os componentes-chave da plataforma BYO-ML, aqui está um exemplo para mostrar como usar a plataforma e os componentes para fornecer uma deteção de ML personalizada.

### <a name="setup-the-databricksspark-environment"></a>Configurar os Databricks/Ambiente de Faíscas

Terá de configurar o seu próprio ambiente Databricks se ainda não tiver um. Consulte o documento [de arranque rápido da Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) para obter instruções.

### <a name="auto-export-instruction"></a>Instrução de exportação automática

Para construir modelos ML personalizados com base nos seus próprios dados no Sentinel, terá de exportar os seus dados do Log Analytics para um armazenamento blob ou recurso de hub de Evento, para que o modelo ML possa aceder aos mesmos a partir de Databricks. Saiba [como ingerir dados no Azure Sentinel.](connect-data-sources.md)

Para este exemplo, precisa de ter os seus dados de formação para o registo de Acesso ao Ficheiro Share no armazenamento de blob Azure. O formato dos dados está documentado no caderno e nas bibliotecas.

Pode exportar automaticamente os seus dados a partir do Log Analytics utilizando a [Interface da Linha de Comando Azure (CLI)](/cli/azure/monitor/log-analytics). 

Tem de ser atribuída a função **Colaboradora** no seu espaço de trabalho Log Analytics, na sua conta de Armazenamento e no seu recurso EventHub para executar os comandos. 

Aqui está um conjunto de conjunto de comandos para configurar a exportação automática:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Exportar dados personalizados

Para dados personalizados que não são suportados pela auto-exportação do Log Analytics, pode utilizar a Logic App ou outras soluções para mover os seus dados. Pode consultar os dados de análise de registo de exportação para o blog e script [da Blob Store.](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true)

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Correlacionar com dados fora de Azure Sentinel

Também pode trazer dados de fora do Azure Sentinel para o armazenamento de bolhas ou Centro de Eventos e correlacioná-los com os dados do Sentinel para construir os seus modelos ML. 
 
### <a name="copy-and-install-the-related-packages"></a>Copiar e instalar os pacotes relacionados

Copie o pacote BYO-ML do repositório Azure Sentinel GitHub mencionado acima para o seu ambiente Databricks. Em seguida, abra os cadernos e siga as instruções dentro do caderno para instalar as bibliotecas necessárias nos seus agrupamentos.

### <a name="model-training-and-scoring"></a>Treinamento de modelo e pontuação

Siga as instruções nos dois cadernos para alterar as configurações de acordo com o seu próprio ambiente e recursos, siga os passos para treinar e construir o seu modelo e, em seguida, agende o modelo para marcar registos de acesso de partilha de ficheiros.

### <a name="write-results-to-log-analytics"></a>Escreva resultados para Registar Analytics

Assim que tiver a pontuação programada, pode utilizar o módulo no caderno de pontuação para escrever os resultados da pontuação no espaço de trabalho Log Analytics associado à sua instância Azure Sentinel.

### <a name="check-results-in-azure-sentinel"></a>Confira os resultados em Azure Sentinel

Para ver os resultados da sua pontuação juntamente com detalhes de registo relacionados, volte ao seu portal Azure Sentinel. Em **Logs** > Registos Personalizados, verá os resultados na tabela **AnomalousResourceAccessResult_CL** (ou no nome da sua própria mesa personalizada). Você pode usar esses resultados para melhorar a sua investigação e experiências de caça.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="registos anómalos de acesso a recursos":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Construa regra de análise personalizada com resultados ML

Uma vez confirmado que os resultados do ML estão na tabela de registos personalizados, e está satisfeito com a fidelidade das pontuações, pode criar uma deteção com base nos resultados. Vá ao **Analytics** a partir do portal Azure Sentinel e [crie uma nova regra de deteção.](tutorial-detect-threats-custom.md) Abaixo está um exemplo que mostra a consulta usada para criar a deteção.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="criar regras de análise personalizadas para deteções de B Y O M L":::

### <a name="view-and-respond-to-incidents"></a>Ver e responder a incidentes
Uma vez configurado a regra de análise com base nos resultados do ML, se houver resultados acima do limiar que definiu na consulta, um incidente será gerado e surgido na página **incidentes** em Azure Sentinel. 

## <a name="next-steps"></a>Próximos passos

Neste documento, aprendeu a usar a plataforma BYO-ML da Azure Sentinel para criar ou importar os seus próprios algoritmos de aprendizagem automática para analisar dados e detetar ameaças.

- Consulte publicações sobre machine learning e muitos outros tópicos relevantes no [Azure Sentinel Blog.](https://aka.ms/azuresentinelblog)