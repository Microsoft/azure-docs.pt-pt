---
title: 'Tutorial: Passe detalhado da Aprendizagem automática na Borda Azure IoT'
description: Um tutorial de alto nível que percorre as várias tarefas necessárias para criar uma aprendizagem automática de ponta a ponta no cenário de ponta a ponta.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 965c420fa29c4cf82517148c01e17d6d7dd6ea97
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74106501"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Tutorial: Uma solução de ponta a ponta utilizando o Azure Machine Learning e o IoT Edge

Frequentemente, as aplicações IoT querem aproveitar a nuvem inteligente e a borda inteligente. Neste tutorial, percorremos-lhe a formação de um modelo de machine learning com dados recolhidos a partir de dispositivos IoT na nuvem, implantando esse modelo para IoT Edge, e mantendo e refinando o modelo periodicamente.

O principal objetivo deste tutorial é introduzir o processamento de dados ioT com machine learning, especificamente no limite. Embora toquemos em muitos aspetos de um fluxo geral de trabalho de aprendizagem automática, este tutorial não se destina a uma introdução aprofundada ao machine learning. Como um caso em particular, não tentamos criar um modelo altamente otimizado para o caso de uso – apenas fazemos o suficiente para ilustrar o processo de criação e utilização de um modelo viável para o processamento de dados IoT.

## <a name="target-audience-and-roles"></a>Público-alvo e papéis

Este conjunto de artigos destina-se a programadores sem experiência prévia em desenvolvimento ioT ou machine learning. Implementar machine learning no limite requer conhecimento de como ligar uma vasta gama de tecnologias. Portanto, este tutorial abrange todo um cenário de ponta a ponta para demonstrar uma forma de juntar estas tecnologias para uma solução IoT. Num ambiente real, estas tarefas podem ser distribuídas por várias pessoas com diferentes especializações. Por exemplo, os desenvolvedores focar-se-iam em qualquer dispositivo ou código de nuvem, enquanto os cientistas de dados conceberam os modelos de análise. Para permitir que um desenvolvedor individual complete com sucesso este tutorial, fornecemos orientações suplementares com insights e ligações a mais informações que esperamos ser suficientes para entender o que está a ser feito, bem como porquê.

Em alternativa, pode juntar-se aos colegas de trabalho de diferentes funções para acompanhar o tutorial em conjunto, reunindo toda a sua experiência e aprendendo como uma equipa como as coisas se encaixam.

Em qualquer dos casos, para ajudar a orientar o(s) leitor( s), cada artigo neste tutorial indica o papel do utilizador. Essas funções incluem:

* Desenvolvimento em nuvem (incluindo um desenvolvedor de nuvem trabalhando em uma capacidade DevOps)
* Análise de dados

## <a name="use-case-predictive-maintenance"></a>Caso de utilização: Manutenção preditiva

Baseamos este cenário num caso de utilização apresentado na Conferência sobre Prognósticos e Gestão da Saúde (PHM08) em 2008. O objetivo é prever a vida útil remanescente (RUL) de um conjunto de motores de avião turbofan. Estes dados foram gerados utilizando o C-MAPSS, a versão comercial do software MAPSS (Simulação modular do sistema aero-propulsão). Este software proporciona um ambiente flexível de simulação do motor turbofan para simular convenientemente os parâmetros de saúde, controlo e motor.

Os dados utilizados neste tutorial são retirados do conjunto de dados de simulação de degradação do [motor Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Do arquivo de leitura:

***Cenário Experimental***

*Os conjuntos de dados consistem em várias séries de tempo multivariadas. Cada conjunto de dados é ainda dividido em subconjuntos de treino e teste. Cada série de cada vez é de um motor diferente – ou seja, os dados podem ser considerados de uma frota de motores do mesmo tipo. Cada motor começa com diferentes graus de desgaste inicial e variação de fabrico que é desconhecido para o utilizador. Este desgaste e variação é considerado normal, ou seja, não é considerado uma condição de falha. Existem três configurações operacionais que têm um efeito substancial no desempenho do motor. Estas definições também estão incluídas nos dados. Os dados estão contaminados com ruído de sensor.*

*O motor está a funcionar normalmente no início de cada série e desenvolve uma falha em algum momento durante a série. No conjunto de treino, a falha cresce em magnitude até falhar no sistema. No conjunto de testes, a série de tempo termina algum tempo antes da falha do sistema. O objetivo do concurso é prever o número de ciclos operacionais restantes antes da avaria no conjunto de testes, ou seja, o número de ciclos operacionais após o último ciclo que o motor continuará a funcionar. Também forneceu um vetor de verdadeiros valores de vida útil remanescente (RUL) para os dados de teste.*

Como os dados foram publicados para um concurso, várias abordagens para derivar modelos de aprendizagem automática foram publicadas de forma independente. Descobrimos que estudar exemplos é útil na compreensão do processo e do raciocínio envolvidos na criação de um modelo específico de aprendizagem automática. Ver, por exemplo:

[Modelo](https://github.com/jancervenka/turbofan_failure) de previsão de falha do motor de aeronaves pelo utilizador do GitHub jancervenka.

[Degradação do motor Turbofan](https://github.com/hankroark/Turbofan-Engine-Degradation) por hankroark utilizador GitHub.

## <a name="process"></a>Processo

A imagem abaixo ilustra os passos ásperos que seguimos neste tutorial:

![Diagrama de arquitetura para passos de processo](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Recolher dados de formação**: O processo começa por recolher dados de formação. Em alguns casos, os dados já foram recolhidos e estão disponíveis numa base de dados, ou sob a forma de ficheiros de dados. Noutros casos, especialmente para cenários ioT, os dados precisam de ser recolhidos a partir de dispositivos e sensores IoT e armazenados na nuvem.

   Assumimos que não tem uma coleção de motores turbofan, pelo que os ficheiros do projeto incluem um simples simulador de dispositivos que envia os dados do dispositivo da NASA para a nuvem.

1. **Preparar dados**. Na maioria dos casos, os dados brutos recolhidos a partir de dispositivos e sensores exigirão preparação para o machine learning. Este passo pode envolver a limpeza de dados, a reformatação de dados ou o pré-processamento para injetar informações adicionais de aprendizagem automática podem ser fundamentadas.

   Para os dados da máquina do motor do avião, a preparação de dados envolve o cálculo de tempo-a-falha explícitos para cada ponto de dados na amostra com base nas observações reais dos dados. Esta informação permite que o algoritmo de aprendizagem automática encontre correlações entre os padrões reais de dados do sensor e o tempo de vida restante esperado do motor. Este passo é altamente específico do domínio.

1. **Construa um modelo de aprendizagem automática.** Com base nos dados preparados, podemos agora experimentar diferentes algoritmos de aprendizagem automática e parametrização para treinar modelos e comparar os resultados uns com os outros.

   Neste caso, para testes comparamos o resultado previsto calculado pelo modelo com o resultado real observado num conjunto de motores. No Azure Machine Learning, podemos gerir as diferentes iterações de modelos que criamos num registo de modelos.

1. **Implementar o modelo**. Assim que tivermos um modelo que satisfaça os nossos critérios de sucesso, podemos passar para a implantação. Isto envolve embrulhar o modelo numa aplicação de serviço web que pode ser alimentada com dados usando chamadas REST e resultados de análise de retorno. A aplicação de serviço web é então embalada num recipiente de estivador, que por sua vez pode ser implantado na nuvem ou como um módulo IoT Edge. Neste exemplo, focamo-nos na implantação para ioT Edge.

1. **Mantenha e refine o modelo.** O nosso trabalho não é feito quando o modelo for implementado. Em muitos casos, queremos continuar a recolher dados e carregar periodicamente esses dados para a nuvem. Podemos então usar estes dados para retreinar e aperfeiçoar o nosso modelo, que depois podemos reimplantar para IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Para completar o tutorial, precisa de acesso a uma subscrição Azure na qual tem direitos de criação de recursos. Vários dos serviços utilizados neste tutorial incorrerão em encargos com o Azure. Se ainda não tiver uma subscrição Azure, poderá começar com uma [Conta Azure Free](https://azure.microsoft.com/offers/ms-azr-0044p/).

Também precisa de uma máquina com powerShell instalada onde pode executar scripts para configurar uma Máquina Virtual Azure como a sua máquina de desenvolvimento.

Neste documento, utilizamos o seguinte conjunto de ferramentas:

* Um hub Azure IoT para captura de dados

* Os Cadernos Azure são a nossa principal extremidade frontal para a preparação de dados e experimentação de machine learning. Executar código python num caderno num subconjunto dos dados da amostra é uma ótima maneira de obter uma reviravolta rápida iterativa e interativa durante a preparação de dados. Os cadernos jupyter também podem ser usados para preparar scripts para correr em escala em um backend computacional.

* Azure Machine Learning como um backend para machine learning em escala e para geração de imagem de aprendizagem automática. Conduzimos o backend Azure Machine Learning usando scripts preparados e testados em cadernos Jupyter.

* Borda Azure IoT para aplicação off-cloud de uma imagem de aprendizagem automática

Obviamente, há outras opções disponíveis. Em certos cenários, por exemplo, a IoT Central pode ser usada como uma alternativa sem código para capturar dados de treino iniciais a partir de dispositivos IoT.

## <a name="next-steps"></a>Passos seguintes

Este tutorial é dividido nas seguintes secções:

1. Instale a sua máquina de desenvolvimento e os serviços Azure.
2. Gere os dados de treino para o módulo de aprendizagem automática.
3. Treine e implante o módulo de aprendizagem automática.
4. Configure um dispositivo IoT Edge para funcionar como uma porta de entrada transparente.
5. Crie e implemente módulos IoT Edge.
6. Envie dados para o seu dispositivo IoT Edge.

Continuar para o próximo artigo para criar uma máquina de desenvolvimento e fornecer recursos Azure.

> [!div class="nextstepaction"]
> [Criar um ambiente para aprendizagem automática em IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
