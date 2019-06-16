---
title: Instruções detalhadas sobre Machine Learning no Azure IoT Edge | Documentos da Microsoft
description: Um tutorial de alto nível que explica as várias tarefas necessárias para criar um ponto-a-ponto, de machine learning no cenário de borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 916e48752431be41ff150c2ac84e66eb1e98e81f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057751"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Tutorial: Uma solução de ponto-a-ponto através do Azure Machine Learning e IoT Edge

Aplicações de IoT querem frequentemente tirar partido da cloud inteligente e de um edge inteligente. Neste tutorial, orientaremos por um modelo de machine learning com dados recolhidos a partir de dispositivos de IoT na cloud, de treinamento implantando esse modelo para o IoT Edge e a manutenção e refinar o modelo periodicamente.

O objetivo principal deste tutorial é apresentar o processamento de dados de IoT com machine learning, especificamente no edge. Enquanto estamos touch muitos aspectos de um fluxo de trabalho de aprendizagem geral, este tutorial não pretende ser uma introdução aprofundada ao machine learning. Como um estudo de caso, não podemos tentar criar um modelo altamente otimizado para o caso de utilização, nós temos apenas suficiente para ilustrar o processo de criar e utilizar um modelo viável para processamento de dados de IoT.

## <a name="target-audience-and-roles"></a>Público-alvo e funções

Este conjunto de artigos destina-se a desenvolvedores sem experiência anterior em aprendizado de máquina de desenvolvimento ou de IoT. Aprendizagem na periferia de implementação automática requer conhecimento de como ligar uma vasta gama de tecnologias. Por conseguinte, este tutorial abrange um cenário ponto-a-ponto completo para demonstrar uma forma de associar estas tecnologias em conjunto para uma solução de IoT. Num ambiente de mundo real, estas tarefas podem ser distribuídas por várias pessoas com especializações diferentes. Por exemplo, os desenvolvedores concentrar no código de dispositivo ou na cloud, enquanto os cientistas de dados criado os modelos de análise. Para permitir que um desenvolvedor individual concluir com êxito este tutorial, nós fornecemos documentação de orientação suplementar com informações e links para obter mais informações, que Esperamos que é suficiente para compreender o que está a ser feito, bem como o porquê.

Em alternativa, pode se juntarem com colegas de trabalho de diferentes funções a seguir o tutorial em conjunto, trazendo seus conhecimentos completo que deve ter e saiba como equipe, como as coisas se encaixam.

Em ambos os casos, para ajudar a orientar a reader(s), cada artigo neste tutorial indica a função do utilizador. Essas funções incluem:

* Desenvolvimento em nuvem (incluindo um desenvolvedor de cloud, trabalhando numa capacidade de DevOps)
* Análise de dados

## <a name="use-case-predictive-maintenance"></a>Caso de utilização: Manutenção preditiva

Estamos com base neste cenário num caso de utilização apresentado na conferência na Prognostics e no gerenciamento de estado de funcionamento (PHM08) 2008. O objetivo é prever a vida útil remanescente (RUL) de um conjunto de motores de avião turbofan. Estes dados foi gerados usando C-MAPSS, versão comercial do software MAPSS (sistema de Aero-impulsionam Propulsion Modular simulação). Este software fornece um ambiente de simulação do motor de turbofan flexível para simular convenientemente o estado de funcionamento, controle e parâmetros de motor.

Os dados utilizados neste tutorial são retirados dos [conjunto de dados de simulação de degradação de motor de Turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

No ficheiro Leia-me:

***Cenário experimental***

*Várias séries de tempo multivariate são compostos por conjuntos de dados. Cada conjunto de dados é dividido em subconjuntos de preparação e teste. Cada série de tempo é de um mecanismo de diferente – ou seja, os dados podem ser considerados para ser de uma frota de mecanismos do mesmo tipo. Cada motor começa com diferentes níveis de desgaste pode eventualmente inicial e a variação que é desconhecida para o utilizador de fabrico. Este desgaste pode eventualmente e variação é considerado normal, ou seja, não é considerada uma condição de falha. Existem três definições operacionais que tem um impacto substancial no desempenho do motor. Estas definições também são incluídas nos dados. Os dados é contaminated com o ruído de sensor.*

*O motor está a funcionar normalmente no início de cada série de tempo e desenvolve uma falha em algum momento durante a série. No conjunto de treinamento, a falha cresce magnitude até que a falha de sistema. O conjunto de teste, a série de tempo termina a algum tempo antes da falha do sistema. O objetivo da competição é prever o número de ciclos operacionais restantes antes da falha no conjunto de teste, ou seja, o número de ciclos operacionais após o último ciclo de que o mecanismo irá continuar a funcionar. Também fornecido um vetor de valores de vida útil remanescente (RUL) verdadeiro para os dados de teste.*

Uma vez que os dados foi publicados numa competição, várias abordagens para derivar de modelos de machine learning foram publicadas independentemente. Descobrimos que estudar os exemplos é útil para entender o processo e raciocinando envolvidos na criação de um modelo específico de machine learning. Veja por exemplo:

[Modelo de predição de falha de motor de aeronave](https://github.com/jancervenka/turbofan_failure) por jancervenka de utilizador do GitHub.

[Degradação de motor de turbofan](https://github.com/hankroark/Turbofan-Engine-Degradation) por hankroark de utilizador do GitHub.

## <a name="process"></a>Process

A figura a seguir ilustra as etapas aproximadas que nós seguir este tutorial:

![Diagrama de arquitetura para obter os passos do processo](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Recolher dados de preparação**: O processo é iniciado através da recolha de dados de treinamento. Em alguns casos, os dados já foi recolhidos e estão disponíveis numa base de dados ou em forma de arquivos de dados. Em outros casos, especialmente nos cenários de IoT, os dados têm de ser recolhidos a partir de sensores e dispositivos IoT e armazenados na nuvem.

   Partimos do princípio de que não tem uma coleção de motores de turbofan, para que os ficheiros de projeto incluem um simulador de dispositivo de simples que envia os dados de dispositivo de NASA para a cloud.

1. **Preparar dados**. Na maioria dos casos, os dados não processados como recolhido a partir de dispositivos e sensores exigirá a preparação para o machine learning. Este passo pode envolver a dados de limpeza, reformatação de dados, ou a pré-processamento de aprendizagem de informações adicionais de injetar pode chave desativado.

   Relativamente aos nossos dados de máquina de motor de avião, a preparação de dados envolve a calcular explícitos tempos de falha de tempo para cada ponto de dados de exemplo com base nas observações reais nos dados. Esta informação permite que o algoritmo de machine learning para descobrir correlações entre os padrões de dados de sensor real e o tempo de vida restante esperado do motor de. Este passo é altamente específicas de domínio.

1. **Criar um modelo do machine learning**. Com base nos dados preparados, pode agora experimentar com algoritmos de aprendizagem diferente e parameterizations para preparar modelos e comparar os resultados entre si.

   Neste caso, para fins de teste, podemos comparar o resultado previsto calculado pelo modelo com o resultado real observado num conjunto de motores de. No Azure Machine Learning, podemos gerir iterações de modelos que foi criar um registo de modelo diferentes.

1. **Implementar o modelo**. Assim que tivermos um modelo que satisfaça os nossos critérios de êxito, podemos avançar para a implementação. Que envolve o modelo de encapsulamento de aplicações numa aplicação de serviço web que pode ser alimentada com dados através de chamadas REST e retornar os resultados da análise. A aplicação de serviço da web, em seguida, é empacotada num contentor do docker, que por sua vez pode ser implementado na cloud ou como um módulo do IoT Edge. Neste exemplo, vamos nos concentrar na implementação para o IoT Edge.

1. **Manter e refinar o modelo**. Nosso trabalho não é feito depois do modelo é implementado. Em muitos casos, o que queremos continuar a recolha de dados e carregar periodicamente os dados para a cloud. Em seguida, podemos usar esses dados para voltar a preparar e refinar o nosso modelo, o que podemos, em seguida, pode Reimplementar no IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, precisa de acesso a uma subscrição do Azure na qual tenha direitos para criar recursos. Vários dos serviços utilizados neste tutorial incorre em encargos do Azure. Se ainda não tiver uma subscrição do Azure, poderá começar com uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Também precisa de uma máquina com o PowerShell instalado em que pode executar scripts para definir a segurança de uma Máquina Virtual da máquina de desenvolvimento.

Neste documento, usamos o seguinte conjunto de ferramentas:

* Um hub IoT do Azure para a captura de dados

* Blocos de notas do Azure como nosso principal front-end para a preparação de dados e aprendizagem automática experimentação. Executar código de python num bloco de notas num subconjunto dos dados de exemplo é uma excelente forma de obter interativo e iterativo rápido o processo durante a preparação de dados. Blocos de notas do Jupyter também podem ser utilizados para preparar a execução em escala num back-end de computação de scripts.

* O Azure Machine Learning como um back-end para a aprendizagem automática à escala e de geração de imagens de aprendizagem automática. Podemos unidade back-end do Azure Machine Learning através de scripts preparado e testadas em blocos de notas do Jupyter.

* O Azure IoT Edge, para a cloud de aplicação de uma imagem do machine learning

Obviamente, existem outras opções disponíveis. Em determinados cenários, por exemplo, IoT Central pode ser utilizado como uma alternativa de sem código para capturar dados de formação inicial dos dispositivos de IoT.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial está dividido nas secções seguintes:

1. Configure a sua máquina de desenvolvimento e a serviços do Azure.
2. Gere os dados de treinamento para o módulo de aprendizagem automática.
3. Preparar e implementar o módulo de aprendizagem de máquina.
4. Configure um dispositivo IoT Edge para atuar como gateway transparente.
5. Criar e implementar módulos do IoT Edge.
6. Envie dados para o seu dispositivo IoT Edge.

Avance para o artigo seguinte para configurar uma máquina de desenvolvimento e aprovisionar recursos do Azure.

> [!div class="nextstepaction"]
> [Configurar um ambiente para aprendizagem do IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
