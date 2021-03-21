---
title: 'Tutorial: Passagem detalhada de machine learning em Azure IoT Edge'
description: Um tutorial de alto nível que percorre as várias tarefas necessárias para criar uma aprendizagem automática de ponta a ponta no cenário de borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67cc470b4f7f119b7f5b86bcb82ea284ab662dfe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463243"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Tutorial: Uma solução de ponta a ponta usando Azure Machine Learning e IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Frequentemente, as aplicações IoT querem aproveitar a nuvem inteligente e a borda inteligente. Neste tutorial, acompanhamos-te através da formação de um modelo de machine learning com dados recolhidos a partir de dispositivos IoT na nuvem, implantando esse modelo para IoT Edge, e mantendo e refinando o modelo periodicamente.

>[!NOTE]
>Os conceitos deste conjunto de tutoriais aplicam-se a todas as versões do IoT Edge, mas o dispositivo de amostra que cria para experimentar o cenário corre a versão 1.1 do IoT Edge.

O objetivo principal deste tutorial é introduzir o processamento de dados de IoT com machine learning, especificamente no limite. Embora toquemos em muitos aspetos de um fluxo de trabalho geral de aprendizagem automática, este tutorial não se destina a uma introdução aprofundada à aprendizagem automática. Como caso em questão, não tentamos criar um modelo altamente otimizado para o caso de utilização – apenas fazemos o suficiente para ilustrar o processo de criação e utilização de um modelo viável para o processamento de dados IoT.

Esta secção do tutorial discute:

> [!div class="checklist"]
>
> * Os pré-requisitos para completar as partes subsequentes do tutorial.
> * O público-alvo do tutorial.
> * O caso de uso que o tutorial simula.
> * O processo geral que o tutorial segue para cumprir o caso de utilização.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar o tutorial, precisa de ter acesso a uma subscrição do Azure na qual tem direitos de criar recursos. Vários dos serviços utilizados neste tutorial incorrerão em encargos da Azure. Se ainda não tiver uma subscrição do Azure, poderá começar com uma [Conta Azure Free](https://azure.microsoft.com/offers/ms-azr-0044p/).

Também precisa de uma máquina com PowerShell instalada onde pode executar scripts para configurar uma Máquina Virtual Azure como máquina de desenvolvimento.

Neste documento, utilizamos o seguinte conjunto de ferramentas:

* Um hub Azure IoT para a captura de dados

* Azure Notebooks como a nossa principal extremidade principal para a preparação de dados e experimentação de machine learning. Executar o código python num caderno num subconjunto dos dados da amostra é uma ótima maneira de obter uma rápida reviravolta iterativa e interativa durante a preparação de dados. Os cadernos Jupyter também podem ser usados para preparar scripts para correr em escala num backend compute.

* Azure Machine Learning como um backend para machine learning em escala e para a geração de imagem de aprendizagem automática. Conduzimos o backend Azure Machine Learning usando scripts preparados e testados em cadernos Jupyter.

* Azure IoT Edge para aplicação off-cloud de uma imagem de aprendizagem automática

Obviamente, existem outras opções disponíveis. Em certos cenários, por exemplo, a IoT Central pode ser usada como uma alternativa sem código para capturar dados de treino iniciais a partir de dispositivos IoT.

## <a name="target-audience-and-roles"></a>Público-alvo e papéis

Este conjunto de artigos destina-se a desenvolvedores sem experiência prévia em desenvolvimento de IoT ou machine learning. A implementação de machine learning no limite requer conhecimento de como ligar uma vasta gama de tecnologias. Portanto, este tutorial abrange todo um cenário de ponta a ponta para demonstrar uma forma de juntar estas tecnologias para uma solução IoT. Num ambiente real, estas tarefas podem ser distribuídas por várias pessoas com diferentes especializações. Por exemplo, os desenvolvedores focavam-se em dispositivos ou códigos de nuvem, enquanto os cientistas de dados conceberam os modelos de análise. Para permitir que um desenvolvedor individual complete este tutorial com sucesso, fornecemos orientações suplementares com insights e ligações a mais informações que esperamos ser suficiente para entender o que está sendo feito, bem como porquê.

Em alternativa, pode juntar-se a colegas de diferentes papéis para seguir o tutorial em conjunto, reunindo toda a sua experiência e aprendendo em equipa como as coisas se encaixam.

Em qualquer dos casos, para ajudar a orientar os leitores, cada artigo neste tutorial indica o papel do utilizador. Essas funções incluem:

* Desenvolvimento em nuvem (incluindo um desenvolvedor de nuvem trabalhando em uma capacidade de DevOps)
* Análise de dados

## <a name="use-case-predictive-maintenance"></a>Caso de utilização: Manutenção preditiva

Baseamos este cenário num caso de utilização apresentado na Conferência sobre Prognósticos e Gestão da Saúde (PHM08) em 2008. O objetivo é prever a vida útil restante (RUL) de um conjunto de motores de avião turbofano. Estes dados foram gerados utilizando o C-MAPSS, a versão comercial do software MAPSS (Modular Aero-Propulsion System Simulation). Este software fornece um ambiente flexível de simulação do motor turbofan para simular convenientemente os parâmetros de saúde, controlo e motor.

Os dados utilizados neste tutorial são retirados do conjunto de dados de simulação de degradação do [motor Turbofan.](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)

Do ficheiro readme:

***Cenário Experimental***

*Os conjuntos de dados consistem em múltiplas séries de tempo multivariadas. Cada conjunto de dados é ainda dividido em subconjuntos de treino e teste. Cada série de tempo é de um motor diferente – ou seja, os dados podem ser considerados de uma frota de motores do mesmo tipo. Cada motor começa com diferentes graus de desgaste inicial e variação de fabrico que é desconhecida para o utilizador. Este desgaste e variação é considerado normal, ou seja, não é considerado uma condição de avaria. Existem três configurações operacionais que têm um efeito substancial no desempenho do motor. Estas definições também estão incluídas nos dados. Os dados estão contaminados com o ruído dos sensores.*

*O motor está a funcionar normalmente no início de cada série de tempo e desenvolve uma falha em algum momento durante a série. No conjunto de treino, a falha cresce em magnitude até falha do sistema. No conjunto de testes, a série de tempo termina algum tempo antes da falha do sistema. O objetivo da competição é prever o número de ciclos operacionais restantes antes da falha no conjunto de ensaios, ou seja, o número de ciclos operacionais após o último ciclo que o motor continuará a operar. Também forneceu um vetor de verdadeiros valores de Vida Útil Restante (RUL) para os dados do teste.*

Como os dados foram publicados para uma competição, várias abordagens para derivar modelos de aprendizagem automática foram publicadas de forma independente. Descobrimos que estudar exemplos é útil para entender o processo e o raciocínio envolvido na criação de um modelo específico de aprendizagem automática. Veja por exemplo:

[Modelo de previsão de falha do motor da aeronave](https://github.com/jancervenka/turbofan_failure) por jancervenka utilizador do GitHub.

[Degradação do motor Turbofan](https://github.com/hankroark/Turbofan-Engine-Degradation) por Hankroark utilizador gitHub.

## <a name="process"></a>Processo

A imagem abaixo ilustra os passos ásperos que seguimos neste tutorial:

![Diagrama de arquitetura para etapas de processo](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Recolher dados de formação**: O processo começa por recolher dados de formação. Em alguns casos, os dados já foram recolhidos e estão disponíveis numa base de dados, ou sob a forma de ficheiros de dados. Noutros casos, especialmente para cenários IoT, os dados devem ser recolhidos a partir de dispositivos e sensores IoT e armazenados na nuvem.

   Assumimos que não tem uma coleção de motores turbofan, por isso os ficheiros do projeto incluem um simples simulador de dispositivo que envia os dados do dispositivo da NASA para a nuvem.

1. **Preparar dados**. Na maioria dos casos, os dados brutos recolhidos a partir de dispositivos e sensores exigirão preparação para a aprendizagem automática. Este passo pode envolver a limpeza de dados, a reformatação de dados ou o pré-processamento para injetar aprendizagem adicional de máquinas de informação pode ser desavetado.

   Para os dados da máquina do nosso motor de avião, a preparação de dados envolve o cálculo de tempos explícitos de tempo para falha para cada ponto de dados da amostra com base nas observações reais dos dados. Esta informação permite que o algoritmo de aprendizagem automática encontre correlações entre os padrões reais de dados do sensor e o tempo de vida remanescente esperado do motor. Este passo é altamente específico do domínio.

1. **Construa um modelo de aprendizagem automática.** Com base nos dados preparados, podemos agora experimentar diferentes algoritmos de aprendizagem automática e parametrizações para formar modelos e comparar os resultados uns com os outros.

   Neste caso, para testes comparamos o resultado previsto calculado pelo modelo com o resultado real observado num conjunto de motores. No Azure Machine Learning, podemos gerir as diferentes iterações de modelos que criamos num registo de modelos.

1. **Implementar o modelo**. Assim que tivermos um modelo que satisfaça os nossos critérios de sucesso, podemos passar para a implantação. Isso envolve envolver o modelo numa aplicação de serviço web que pode ser alimentada com dados usando chamadas REST e resultados de análise de devolução. A aplicação de serviço web é então embalada num recipiente de estivadores, que por sua vez pode ser implantado na nuvem ou como um módulo IoT Edge. Neste exemplo, focamo-nos na implantação para ioT Edge.

1. **Mantenha e refine o modelo.** O nosso trabalho não é feito uma vez que o modelo é implementado. Em muitos casos, queremos continuar a recolher dados e enviar periodicamente esses dados para a nuvem. Podemos então usar estes dados para retreinar e aperfeiçoar o nosso modelo, que depois podemos recolocar para ioT Edge.

## <a name="clean-up-resources"></a>Limpar os recursos

Este tutorial faz parte de um conjunto onde cada artigo baseia-se no trabalho feito nos anteriores. Por favor, espere para limpar todos os recursos até completar o tutorial final.

## <a name="next-steps"></a>Passos seguintes

Este tutorial é dividido nas seguintes secções:

1. Confende a sua máquina de desenvolvimento e os serviços Azure.
2. Gere os dados de treino para o módulo de aprendizagem automática.
3. Treine e implante o módulo de aprendizagem automática.
4. Configure um dispositivo IoT Edge para funcionar como uma porta de entrada transparente.
5. Criar e implementar módulos IoT Edge.
6. Envie dados para o seu dispositivo IoT Edge.

Continue até ao próximo artigo para criar uma máquina de desenvolvimento e providenciar recursos Azure.

> [!div class="nextstepaction"]
> [Crie um ambiente para aprendizagem automática em IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)