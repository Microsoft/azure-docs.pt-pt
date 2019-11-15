---
title: 'Tutorial: explicação detalhada de Machine Learning em Azure IoT Edge'
description: Um tutorial de alto nível que percorre as várias tarefas necessárias para criar um aprendizado de máquina de ponta a ponta no cenário de borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 965c420fa29c4cf82517148c01e17d6d7dd6ea97
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106501"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Tutorial: uma solução de ponta a ponta usando Azure Machine Learning e IoT Edge

Frequentemente, os aplicativos de IoT desejam aproveitar a nuvem inteligente e a borda inteligente. Neste tutorial, orientaremos você pelo treinamento de um modelo de aprendizado de máquina com dados coletados de dispositivos IoT na nuvem, implantando esse modelo para IoT Edge e mantendo e refinando o modelo periodicamente.

O objetivo principal deste tutorial é apresentar o processamento de dados de IoT com o aprendizado de máquina, especificamente na borda. Embora tenhamos contato com vários aspectos de um fluxo de trabalho de aprendizado de máquina geral, este tutorial não se destina a uma introdução aprofundada ao aprendizado de máquina. Como um caso no ponto, não tentamos criar um modelo altamente otimizado para o caso de uso – nós apenas fazemos o suficiente para ilustrar o processo de criação e uso de um modelo viável para o processamento de dados de IoT.

## <a name="target-audience-and-roles"></a>Público-alvo e funções

Este conjunto de artigos destina-se a desenvolvedores sem experiência anterior em desenvolvimento de IoT ou aprendizado de máquina. A implantação do Machine Learning na borda exige o conhecimento de como conectar uma ampla variedade de tecnologias. Portanto, este tutorial aborda um cenário completo de ponta a ponta para demonstrar uma maneira de unir essas tecnologias em uma solução de IoT. Em um ambiente do mundo real, essas tarefas podem ser distribuídas entre várias pessoas com especialidades diferentes. Por exemplo, os desenvolvedores se concentrariam no código do dispositivo ou na nuvem, enquanto os cientistas de dados criaram os modelos de análise. Para permitir que um desenvolvedor individual conclua este tutorial com êxito, fornecemos diretrizes complementares com ideias e links para mais informações que esperamos que seja suficiente para entender o que está sendo feito, bem como o porquê.

Como alternativa, você pode fazer uma parceria com colegas de funções diferentes para acompanhar o tutorial, trazendo sua experiência completa e aprender como uma equipe como as coisas se encaixam.

Em ambos os casos, para ajudar a orientar o (s) leitor (es), cada artigo neste tutorial indica a função do usuário. Essas funções incluem:

* Desenvolvimento em nuvem (incluindo um desenvolvedor de nuvem trabalhando em uma capacidade DevOps)
* Análise de dados

## <a name="use-case-predictive-maintenance"></a>Caso de uso: manutenção preditiva

Baseiamos esse cenário em um caso de uso apresentado na conferência sobre o PHM08 (Prognostics and Health Management) em 2008. O objetivo é prever a vida útil restante (RUL) de um conjunto de motores de avião turbofan. Esses dados foram gerados usando mapas C, a versão comercial dos mapas (software modular Aero-propulsão System Simulation). Este software fornece um ambiente de simulação de mecanismo turbofan flexível para simular convenientemente os parâmetros de integridade, controle e mecanismo.

Os dados usados neste tutorial são obtidos do conjunto de [dados de simulação de degradação do mecanismo de turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

No arquivo Leiame:

***Cenário experimental***

*Conjuntos de dados consistem em várias séries de tempo MultiVariable. Cada conjunto de dados é dividido em subconjuntos de treinamento e teste. Cada série temporal é de um mecanismo diferente – ou seja, os dados podem ser considerados de uma frota de mecanismos do mesmo tipo. Cada mecanismo começa com diferentes graus de desgaste inicial e variação de fabricação que é desconhecida para o usuário. Esse desgaste e variação é considerado normal, ou seja, não é considerado uma condição de falha. Há três configurações operacionais que têm um efeito significativo sobre o desempenho do mecanismo. Essas configurações também estão incluídas nos dados. Os dados são contaminados com ruído de sensor.*

*O mecanismo está operando normalmente no início de cada série temporal e desenvolve uma falha em algum momento durante a série. No conjunto de treinamento, a falha aumenta em magnitude até a falha do sistema. No conjunto de teste, a série temporal termina algum tempo antes da falha do sistema. O objetivo da competição é prever o número de ciclos operacionais restantes antes da falha no conjunto de teste, ou seja, o número de ciclos operacionais após o último ciclo que o mecanismo continuará a operar. Também forneceu um vetor de valores reais restantes de RUL (vida útil) para os dados de teste.*

Como os dados foram publicados para uma competição, várias abordagens para derivar modelos de aprendizado de máquina foram publicadas de forma independente. Descobrimos que os exemplos de estudo são úteis para entender o processo e o raciocínio envolvidos na criação de um modelo de aprendizado de máquina específico. Consulte, por exemplo:

[Modelo de previsão de falha do mecanismo de aeronave](https://github.com/jancervenka/turbofan_failure) por jancervenka de usuário do github.

[Degradação do mecanismo turbofan](https://github.com/hankroark/Turbofan-Engine-Degradation) pelo usuário do GitHub hankroark.

## <a name="process"></a>Processo

A figura abaixo ilustra as etapas aproximadas a seguir neste tutorial:

![Diagrama de arquitetura para etapas do processo](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Coletar dados de treinamento**: o processo começa coletando dados de treinamento. Em alguns casos, os dados já foram coletados e estão disponíveis em um banco de dados, ou na forma de arquivos. Em outros casos, especialmente para cenários de IoT, os dados precisam ser coletados de dispositivos e sensores de IoT e armazenados na nuvem.

   Supomos que você não tenha uma coleção de mecanismos de turbofan, portanto, os arquivos de projeto incluem um simulador de dispositivo simples que envia os dados do dispositivo de NASA para a nuvem.

1. **Preparar dados**. Na maioria dos casos, os dados brutos, conforme coletados de dispositivos e sensores, exigirão preparação para o aprendizado de máquina. Esta etapa pode envolver a limpeza de dados, a reformatação de dados ou o pré-processamento para injetar informações adicionais que o Machine Learning pode desativar.

   Para nossos dados de computador do Engine de avião, a preparação de dados envolve calcular tempos de tempo para falhas explícitos para cada ponto de dados na amostra com base nas observações reais sobre os dados. Essas informações permitem que o algoritmo de aprendizado de máquina Localize correlações entre padrões de dados de sensor reais e o tempo de vida restante esperado do mecanismo. Esta etapa é altamente específica do domínio.

1. **Crie um modelo de aprendizado de máquina**. Com base nos dados preparados, agora podemos experimentar diferentes algoritmos de aprendizado de máquina e parametrizações para treinar modelos e comparar os resultados entre si.

   Nesse caso, para teste, comparamos o resultado previsto calculado pelo modelo com o resultado real observado em um conjunto de mecanismos. No Azure Machine Learning, podemos gerenciar as diferentes iterações de modelos que criamos em um registro de modelo.

1. **Implante o modelo**. Assim que tivermos um modelo que satisfaça nossos critérios de sucesso, podemos mudar para a implantação. Isso envolve o encapsulamento do modelo em um aplicativo de serviço Web que pode ser alimentado com dados usando chamadas REST e resultados de análise de retorno. O aplicativo de serviço Web é então empacotado em um contêiner do Docker que, por sua vez, pode ser implantado na nuvem ou como um módulo IoT Edge. Neste exemplo, nos concentramos na implantação para IoT Edge.

1. **Manter e refinar o modelo**. Nosso trabalho não é feito depois que o modelo é implantado. Em muitos casos, queremos continuar coletando dados e carregar periodicamente esses dados para a nuvem. Em seguida, podemos usar esses dados para treinar novamente e refinar nosso modelo, que podemos reimplantar para IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, você precisa ter acesso a uma assinatura do Azure na qual você tenha direitos para criar recursos. Vários dos serviços usados neste tutorial incorrerão em cobranças do Azure. Se você ainda não tiver uma assinatura do Azure, talvez seja possível começar com uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Você também precisa de um computador com o PowerShell instalado, no qual você pode executar scripts para configurar uma máquina virtual do Azure como seu computador de desenvolvimento.

Neste documento, usamos o seguinte conjunto de ferramentas:

* Um hub IoT do Azure para captura de dados

* Azure Notebooks como nosso front-end principal para preparação de dados e experimentação de aprendizado de máquina. Executar o código Python em um bloco de anotações em um subconjunto dos dados de exemplo é uma ótima maneira de obter um retorno rápido e interativo iterativo durante a preparação dos dados. Os notebooks Jupyter também podem ser usados para preparar scripts para serem executados em escala em um back-end de computação.

* Azure Machine Learning como um back-end para aprendizado de máquina em escala e para geração de imagem de aprendizado de máquina. Nós orientamos o back-end de Azure Machine Learning usando scripts preparados e testados em notebooks Jupyter.

* Azure IoT Edge para o aplicativo fora da nuvem de uma imagem de aprendizado de máquina

Obviamente, há outras opções disponíveis. Em determinados cenários, por exemplo, IoT Central pode ser usado como uma alternativa sem código para capturar dados de treinamento iniciais de dispositivos IoT.

## <a name="next-steps"></a>Passos seguintes

Este tutorial é dividido nas seguintes seções:

1. Configure seu computador de desenvolvimento e os serviços do Azure.
2. Gere os dados de treinamento para o módulo de aprendizado de máquina.
3. Treine e implante o módulo de aprendizado de máquina.
4. Configure um dispositivo IoT Edge para atuar como um gateway transparente.
5. Criar e implantar módulos de IoT Edge.
6. Envie dados para o dispositivo de IoT Edge.

Continue no próximo artigo para configurar um computador de desenvolvimento e provisionar recursos do Azure.

> [!div class="nextstepaction"]
> [Configurar um ambiente para aprendizado de máquina no IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
