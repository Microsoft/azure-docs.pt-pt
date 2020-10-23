---
title: Paridade entre regiões públicas e soberanas
titleSuffix: Azure Machine Learning
description: Algumas funcionalidades do Azure Machine Learning, como as funcionalidades de pré-visualização do público, só podem estar disponíveis em regiões de nuvem pública. Este artigo lista as funcionalidades também disponíveis nas regiões Azure, Azure Germany e Azure China 21Vianet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 08/24/2020
ms.custom: references_regions
ms.openlocfilehash: ddfe1344b235500f0f1ea69b6e3d8c537f96b855
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426527"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure Machine Learning paridade de nuvem soberana

Saiba quais as funcionalidades de Aprendizagem automática Azure disponíveis em regiões de nuvem soberana. 

Na lista das regiões globais do Azure, existem várias regiões "soberanas" que servem mercados específicos. Por exemplo, o Governo de Azure e as regiões Azure China 21Vianet. Atualmente, o Azure Machine Learning é implantado nas seguintes regiões de nuvem soberana:

* Regiões do Governo de Azure **EUA-Arizona** e **EUA-Virgínia.**
* Azure China 21Vianet região **China-Leste-2**.

> [!TIP]
> Para diferenciar as regiões soberanas e não-soberanas, este artigo utilizará o termo __nuvem pública__ para se referir a regiões não-soberanas.

Pretendemos proporcionar a máxima paridade entre a nossa nuvem pública e as regiões soberanas. Todas as funcionalidades de Machine Learning do Azure estarão disponíveis nestas regiões dentro **de 30 dias de GA** (disponibilidade geral) na nossa nuvem pública. Também permitimos um número selecionado de funcionalidades de pré-visualização nestas regiões. Abaixo mostram as diferenças de paridade atuais entre as nossas nuvens soberanas e públicas.

## <a name="azure-government"></a>Azure Government 

| Funcionalidade | Estado da nuvem pública  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **Aprendizagem automática automatizada** | | | |
| Criar e executar experiências em cadernos                                    | GA                   | SIM                | SIM         |
| Criar e executar experiências em experiência web de estúdio                        | Pré-visualização pública       | SIM                | SIM         |
| Capacidades de previsão líderes do setor                                  | GA                   | SIM                | SIM         |
| Apoio à aprendizagem profunda e a outros alunos avançados                      | GA                   | SIM                | SIM         |
| Grande suporte a dados (até 100 GB)                                          | Pré-visualização pública       | SIM                | SIM         |
| Integração de Azure Databricks                                              | GA                   | NO                 | NO          |
| Integrações SQL, CosmosDB e HDInsight                                   | GA                   | SIM                | SIM         |
| **Gasodutos de Aprendizagem automática** |   |  | | 
| Criar, executar e publicar oleodutos utilizando o Azure ML SDK                   | GA                   | SIM                | SIM         |
| Criar pontos finais de gasodutos utilizando o Azure ML SDK                           | GA                   | SIM                | SIM         |
| Criar, editar e apagar as execuções programadas de oleodutos utilizando o Azure ML SDK | GA                   | SIM*               | SIM*        |
| Ver detalhes de execução do pipeline em estúdio                                        | GA                   | SIM                | SIM         |
| Criar, executar, visualizar e publicar oleodutos no designer Azure ML          | GA      | SIM                | SIM         |
| Integração de tijolos de dados Azure com pipeline ML                             | GA                   | NO                 | NO          |
| Criar pontos finais de gasodutos em Azure ML designer                             | GA      | SIM                | SIM         |
| **Cadernos integrados** |   |  | | 
| Notebook do espaço de trabalho e partilha de arquivos                                        | GA                   | SIM                | SIM         |
| Apoio R e Python                                                       | GA                   | SIM                | SIM         |
| Suporte de Rede Virtual                                                    | Pré-visualização pública       | NO                 | NO          |
| **Instância computacional** |   |  | | 
| Casos de computação gerido para cadernos integrados                         | GA                   | SIM                | SIM         |
| Jupyter, Integração jupyterLab                                            | GA                   | SIM                | SIM         |
| Suporte de Rede Virtual (VNet)                                             | Pré-visualização pública       | SIM                | SIM         |
| **Suporte de SKDs** |  |  | | 
| Suporte R SDK                                                              | Pré-visualização pública       | SIM                | SIM         |
| Suporte Python SDK                                                         | GA                   | SIM                | SIM         |
| **Segurança** |   | | | 
| Suporte de Rede Virtual (VNet) para formação                                | GA                   | SIM                | SIM         |
| Suporte de Rede Virtual (VNet) para inferência                               | GA                   | SIM                | SIM         |
| Autenticação de ponto final de pontuação                                            | Pré-visualização pública       | SIM                | SIM         |
| Ligação privada no local de trabalho                                                     | Pré-visualização pública       | NO                 | NO          |
| ACI atrás do VNet                                                            | Pré-visualização pública       | NO                 | NO          |
| ACR atrás do VNet                                                            | Pré-visualização pública       | NO                 | NO          |
| IP privado do cluster AKS                                                  | Pré-visualização pública       | NO                 | NO          |
| **Computação** |   | | |
| gestão de quotas em todos os espaços de trabalho                                         | GA                   | SIM                | SIM         |
| **Dados para aprendizagem automática** |   | | |
| Criar, ver ou editar conjuntos de dados e datas do SDK                  | GA                   | SIM                | SIM         |
| Criar, ver ou editar conjuntos de dados e datas da UI                   | GA                   | SIM                | SIM         |
| Ver, editar ou eliminar monitores de deriva do conjunto de dados do SDK                   | Pré-visualização pública       | SIM                | SIM         |
| Ver, editar ou eliminar monitores de deriva do conjunto de dados da UI                    | Pré-visualização pública       | SIM                | SIM         |
| **Ciclo de vida de aprendizagem automática** |   | | |
| Perfis de modelos                                                            | GA                   | SIM                | PARCIAL     |
| A extensão Azure DevOps para machine learning & o Azure ML CLI         | GA                   | SIM                | SIM         |
| Modelos acelerados de hardware baseados na FPGA                                     | GA                   | NO                 | NO          |
| Integração visual do Código do Estúdio                                             | Pré-visualização pública       | NO                 | NO          |
| Integração do Event Grid                                                     | Pré-visualização pública       | NO                 | NO          |
| Integre a Azure Stream Analytics com Azure Machine Learning               | Pré-visualização pública       | NO                 | NO          |
| **Rotulagem** |   | | |
| Portal de Gestão de Projetos de Rotulagem                                        | GA                   | SIM                | SIM         |
| Portal do Rótulo                                                            | GA                   | SIM                | SIM         |
| Rotulagem utilizando mão de obra privada                                          | GA                   | SIM                | SIM         |
| Rotulagem assistida ML (classificação de imagem e deteção de objetos)           | Pré-visualização pública       | SIM                | SIM         |
| **ML responsável** |   | | |
| Explicabilidade na UI                                                       | Pré-visualização pública       | NO                 | NO          |
| Kit de ferramentas whiteNoise de privacidade diferencial                                    | OSS                  | NO                 | NO          |
| tags personalizados em Azure Machine Learning para implementar folhas de dados              | GA                   | NO                 | NO          |
| Integração AzureML da Justiça                                               | Pré-visualização pública       | NO                 | NO          |
| SDK de interpretação                                                      | GA                   | SIM                | SIM         |
| **Formação** |   | | |
| Streaming de registos de experimentação                                              | GA                   | SIM                | SIM         |
| Aprendizagem de Reforço                                                     | Pré-visualização pública       | NO                 | NO          |
| UI de experimentação                                                         | GA                   | SIM                | SIM         |
| .NET integração ML.NET 1.0                                                | GA                   | SIM                | SIM         |
| **Inferência** |   | | |
| Inferenculação de lote                                                          | GA                   | SIM                | SIM         |
| Borda da Caixa de Dados com FPGA                                                    | Pré-visualização pública       | NO                 | NO          |
| **Outro** |   | | |
| Open Datasets                                                              | Pré-visualização pública       | SIM                | SIM         |
| Pesquisa Cognitiva Personalizada                                                    | Pré-visualização pública       | SIM                | SIM         |
| Muitos Modelos                                                                | Pré-visualização pública       | NO                 | NO          |


### <a name="azure-government-scenarios"></a>Cenários do Governo de Azure

| Cenário                                                    | US-Virginia | US-Arizona| Limitações  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Configuração geral de segurança** |   | | |
| Comunicação de rede privada entre serviços                                     | NO | NO | Nenhuma ligação privada atualmente | 
| Desativar/controlar o acesso à Internet (entrada e saída) e vNet específico | PARCIAL| PARCIAL   | ACR por trás da VNet não está disponível no Governo Azure - verificação dupla no ACI | 
| Colocação para todos os recursos/serviços associados  | SIM | SIM |  |
| Encriptação em repouso e em trânsito.                                                 | SIM | SIM |  |
| Root e SSH acesso a recursos computetivos.                                          | SIM | SIM |  |
| Manter a segurança dos sistemas implantados (instâncias, pontos finais, etc.), incluindo proteção de pontos finais, remendos e registos |  PARCIAL|  PARCIAL |ACI por trás do VNet e ponto final privado atualmente não disponível |                                  
| Controlo (desativação/limite/restrição) da utilização da integração ACI/AKS                    | PARCIAL| PARCIAL |ACI por trás do VNet e ponto final privado atualmente não disponível|
| Azure controle de acesso baseado em funções (Azure RBAC) - Criações de função personalizada                           | SIM | SIM |  |
| Controle o acesso às imagens ACR utilizadas pelo ML Service (Azure fornecido/mantido versus personalizado)  |PARCIAL|  PARCIAL | ACR por trás de ponto final privado e VNet não apoiado no Governo de Azure |
| **Utilização geral do serviço de aprendizagem automática** |  | | |
| Capacidade de ter um ambiente de desenvolvimento para construir um modelo, treinar esse modelo, acolhê-lo como um ponto final, e consumi-lo através de um webapp     | SIM | SIM |  |
| Capacidade de retirar dados do ADLS (Data Lake Storage)                                 |SIM | SIM |  |
| Capacidade de retirar dados do Azure Blob Storage                                       |SIM | SIM |  |



### <a name="additional-azure-government-limitations"></a>Limitações adicionais do Governo Azure

* Para os casos de cálculo da Azure Machine Learning, a capacidade de refrescar um símbolo com uma duração superior a 24 horas não está disponível no Governo de Azure.
* O Model Profiling não suporta 4 CPUs na região US-Arizona.   
* As amostras de cadernos podem não funcionar no Governo de Azure se precisar de acesso aos dados públicos.
* Endereços IP: O comando CLI utilizado no VNet e instruções [de túneis forçados](how-to-secure-training-vnet.md#forced-tunneling) não devolve os intervalos IP. Utilize as [gamas IP Azure e as etiquetas de serviço para o Governo Azure.](https://www.microsoft.com/download/details.aspx?id=57063)
* Para os oleodutos programados, também fornecemos um mecanismo de gatilho baseado em bolhas. Este mecanismo não é suportado para espaços de trabalho CMK. Para ativar um gatilho à base de bolhas para espaços de trabalho CMK, tem de fazer uma configuração adicional. Para obter mais informações, consulte [Trigger uma série de um pipeline de aprendizagem automática a partir de uma Aplicação Lógica](how-to-trigger-published-pipeline.md).
* Firewalls: Quando utilizar uma região do Governo Azure, adicione os seguintes anfitriões adicionais à sua definição de firewall:

    * Para uso no Arizona: `usgovarizona.api.ml.azure.us`
    * Para uso na Virgínia: `usgovvirginia.api.ml.azure.us`
    * Para ambos: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure China 21Vianet 

| Funcionalidade                                       | Estado da nuvem pública | CH-East-2 | CH-Norte-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Aprendizagem automática automatizada** |    | | |
| Criar e executar experiências em cadernos                                    | GA               | SIM       | N/D        |
| Criar e executar experiências em experiência web de estúdio                        | Pré-visualização pública   | SIM       | N/D        |
| Capacidades de previsão líderes do setor                                  | GA               | SIM       | N/D        |
| Apoio à aprendizagem profunda e a outros alunos avançados                      | GA               | SIM       | N/D        |
| Grande suporte a dados (até 100 GB)                                          | Pré-visualização pública   | SIM       | N/D        |
| Integração de Tijolos de Dados Azure                                              | GA               | NO        | N/D        |
| Integrações SQL, CosmosDB e HDInsight                                   | GA               | SIM       | N/D        |
| **Gasodutos de Aprendizagem automática** |    | | |
| Criar, executar e publicar oleodutos utilizando o Azure ML SDK                   | GA               | SIM       | N/D        |
| Criar pontos finais de gasodutos utilizando o Azure ML SDK                           | GA               | SIM       | N/D        |
| Criar, editar e apagar as execuções programadas de oleodutos utilizando o Azure ML SDK | GA               | SIM       | N/D        |
| Ver detalhes de execução do pipeline em estúdio                                        | GA               | SIM       | N/D        |
| Criar, executar, visualizar e publicar oleodutos no designer Azure ML          | GA  | SIM       | N/D        |
| Integração de tijolos de dados Azure com pipeline ML                             | GA               | NO        | N/D        |
| Criar pontos finais de gasodutos em Azure ML designer                             | GA   | SIM       | N/D        |
| **Cadernos integrados** |   | | |
| Notebook do espaço de trabalho e partilha de arquivos                                        | GA               | SIM       | N/D        |
| Apoio R e Python                                                       | GA               | SIM       | N/D        |
| Suporte de Rede Virtual                                                    | Pré-visualização pública   | NO        | N/D        |
| **Instância computacional** |    | | |
| Casos de computação gerido para cadernos integrados                         | GA               | NO        | N/D        |
| Jupyter, Integração jupyterLab                                            | GA               | SIM       | N/D        |
| Suporte de Rede Virtual (VNet)                                             | Pré-visualização pública   | SIM       | N/D        |
| **Suporte de SKDs** |    | | |
| Suporte R SDK                                                              | Pré-visualização pública   | SIM       | N/D        |
| Suporte Python SDK                                                         | GA               | SIM       | N/D        |
| **Segurança** |   | | |
| Suporte de Rede Virtual (VNet) para formação                                | GA               | SIM       | N/D        |
| Suporte de Rede Virtual (VNet) para inferência                               | GA               | SIM       | N/D        |
| Autenticação de ponto final de pontuação                                            | Pré-visualização pública   | SIM       | N/D        |
| Ligação privada no local de trabalho                                                     | Pré-visualização pública   | NO        | N/D        |
| ACI atrás do VNet                                                            | Pré-visualização pública   | NO        | N/D        |
| ACR atrás do VNet                                                            | Pré-visualização pública   | NO        | N/D        |
| IP privado do cluster AKS                                                  | Pré-visualização pública   | NO        | N/D        |
| **Computação** |   | | |
| gestão de quotas em todos os espaços de trabalho                                         | GA               | SIM       | N/D        |
| **Dados para aprendizagem automática** | | | |
| Criar, ver ou editar conjuntos de dados e datas do SDK                  | GA               | SIM       | N/D        |
| Criar, ver ou editar conjuntos de dados e datas da UI                   | GA               | SIM       | N/D        |
| Ver, editar ou eliminar monitores de deriva do conjunto de dados do SDK                   | Pré-visualização pública   | SIM       | N/D        |
| Ver, editar ou eliminar monitores de deriva do conjunto de dados da UI                    | Pré-visualização pública   | SIM       | N/D        |
| **Ciclo de vida de aprendizagem automática** |    | | |
| Perfis de modelos                                                            | GA               | PARCIAL   | N/D        |
| A extensão Azure DevOps para machine learning & o Azure ML CLI         | GA               | SIM       | N/D        |
| Modelos acelerados de hardware baseados na FPGA                                     | GA               | NO        | N/D        |
| Integração visual do Código do Estúdio                                             | Pré-visualização pública   | NO        | N/D        |
| Integração do Event Grid                                                     | Pré-visualização pública   | SIM       | N/D        |
| Integre a Azure Stream Analytics com Azure Machine Learning               | Pré-visualização pública   | NO        | N/D        |
| **Rotulagem** |    | | |
| Portal de Gestão de Projetos de Rotulagem                                        | GA               | SIM       | N/D        |
| Portal do Rótulo                                                            | GA               | SIM       | N/D        |
| Rotulagem utilizando mão de obra privada                                          | GA               | SIM       | N/D        |
| Rotulagem assistida ML (classificação de imagem e deteção de objetos)           | Pré-visualização pública   | SIM       | N/D        |
| **ML responsável** |    | | |
| Explicabilidade na UI                                                       | Pré-visualização pública   | NO        | N/D        |
| Kit de ferramentas whiteNoise de privacidade diferencial                                    | OSS              | NO        | N/D        |
| tags personalizados em Azure Machine Learning para implementar folhas de dados              | GA               | NO        | N/D        |
| Integração AzureML da Justiça                                               | Pré-visualização pública   | NO        | N/D        |
| SDK de interpretação                                                      | GA               | SIM       | N/D        |
| **Formação** |    | | |
| Streaming de registos de experimentação                                              | GA               | SIM       | N/D        |
| Aprendizagem de Reforço                                                     | Pré-visualização pública   | NO        | N/D        |
| UI de experimentação                                                         | GA               | SIM       | N/D        |
| .NET integração ML.NET 1.0                                                | GA               | SIM       | N/D        |
| **Inferência** |   | | |
| Inferenculação de lote                                                          | GA               | SIM       | N/D        |
| Borda da Caixa de Dados com FPGA                                                    | Pré-visualização pública   | NO        | N/D        |
| **Outro** |    | | |
| Open Datasets                                                              | Pré-visualização pública   | SIM       | N/D        |
| Pesquisa Cognitiva Personalizada                                                    | Pré-visualização pública   | SIM       | N/D        |
| Muitos Modelos                                                                | Pré-visualização pública   | NO        | N/D        |



### <a name="additional-azure-china-limitations"></a>Limitações adicionais da Azure China

* A Azure China tem VM SKU limitado, especialmente para GPU SKU. Tem apenas família NCv3 (V100).
* REST API Endpoints são diferentes do Azure global. Utilize o seguinte quadro para encontrar o ponto final da API REST para as regiões da Azure China:

    | Ponto final DE REPOUSO                 | Global Azure                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Plano de gestão | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Plano de dados       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* O caderno de amostras pode não funcionar, se precisar de acesso aos dados públicos.
* Intervalos de endereços IP: O comando CLI utilizado nas instruções [de túneis forçados VNet](how-to-secure-training-vnet.md#forced-tunneling) não devolve os intervalos IP. Utilize as [gamas IP Azure e tags de serviço para a Azure China.](https://www.microsoft.com//download/details.aspx?id=57062)
* A pré-visualização de instâncias computacional de aprendizagem automática Azure não é suportada num espaço de trabalho onde o Private Link está habilitado para já, mas o CI será suportado na próxima implementação para a expansão do serviço para todas as regiões AML.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as regiões em que o Azure Machine learning está disponível, consulte [Produtos por região.](https://azure.microsoft.com/global-infrastructure/services/)
