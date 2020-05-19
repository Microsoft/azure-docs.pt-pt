---
title: Implementar privacidade diferencial com o pacote WhiteNoise
titleSuffix: Azure Machine Learning
description: Saiba o que é privacidade diferencial e como o pacote WhiteNoise pode ajudá-lo a implementar sistemas privados diferenciais que preservem a privacidade dos dados.
author: luisquintanilla
ms.author: luquinta
ms.date: 05/03/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.openlocfilehash: 09ac31f31f42f5aed9e7dd464e1fce1436cfe581
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597647"
---
# <a name="preserve-data-privacy-by-using-differential-privacy-and-the-whitenoise-package"></a>Preservar a privacidade dos dados utilizando a privacidade diferencial e o pacote WhiteNoise

Saiba o que é privacidade diferencial e como o pacote WhiteNoise pode ajudá-lo a implementar sistemas diferenciais privados.

À medida que a quantidade de dados que uma organização recolhe e utiliza para análises aumenta, também as preocupações com a privacidade e segurança aumentam. As análises requerem dados. Normalmente, quanto mais dados usados para treinar modelos, mais precisos são. Quando as informações pessoais são usadas para estas análises, é especialmente importante que os dados se mantenham privados em toda a sua utilização.

## <a name="how-differential-privacy-works"></a>Como funciona a privacidade diferencial

A privacidade diferencial é um conjunto de sistemas e práticas que ajudam a manter os dados dos indivíduos seguros e privados.

> [!div class="mx-imgBorder"]
> ![Processo de privacidade diferencial](./media/concept-differential-privacy/differential-privacy-process.jpg)

Nos cenários tradicionais, os dados brutos são armazenados em ficheiros e bases de dados. Quando os utilizadores analisam dados, normalmente utilizam os dados brutos. Esta é uma preocupação porque pode infringir a privacidade de um indivíduo. A privacidade diferencial tenta lidar com este problema adicionando "ruído" ou aleatoriedade aos dados para que os utilizadores não possam identificar quaisquer pontos de dados individuais. Pelo menos, tal sistema proporciona uma negação plausível.

Em sistemas diferenciais privados, os dados são partilhados através de pedidos **chamados consultas**. Quando um utilizador submete uma consulta de dados, as operações conhecidas como **mecanismos** de privacidade adicionam ruído aos dados solicitados. Os mecanismos de privacidade devolvem uma *aproximação dos dados* em vez dos dados brutos. Este resultado de preservação da privacidade aparece num **relatório**. Os relatórios consistem em duas partes, os dados reais calculados e uma descrição de como os dados foram criados.

## <a name="differential-privacy-metrics"></a>Métricas de privacidade diferenciais

A privacidade diferencial tenta proteger contra a possibilidade de um utilizador poder produzir um número indefinido de relatórios para eventualmente revelar dados sensíveis. Um valor conhecido como **epsilon** mede o quão ruidoso ou privado é um relatório. Epsilon tem uma relação inversa com o ruído ou privacidade. Quanto mais baixo o epsilon, mais ruidoso (e privado) os dados são.

Os valores de Epsilon não são negativos. Valores abaixo de 1 proporcionam negação plausível total. Qualquer coisa acima de 1 vem com um maior risco de exposição dos dados reais. Ao implementar sistemas diferenciais privados, pretende produzir relatórios com valores epsilon entre 0 e 1.

Outro valor diretamente correlacionado com a epsilon é **delta**. A Delta é uma medida da probabilidade de um relatório não ser totalmente privado. Quanto maior o delta, maior o epsilon. Como estes valores estão correlacionados, o epsilon é usado com mais frequência.

## <a name="privacy-budget"></a>Orçamento de privacidade

Para garantir a privacidade nos sistemas onde são permitidas múltiplas consultas, a privacidade diferencial define um limite de taxa. Este limite é conhecido como um orçamento de **privacidade.** Os orçamentos de privacidade são atribuídos a um montante epsilon, tipicamente entre 1 e 3 para limitar o risco de reidentificação. À medida que os relatórios são gerados, os orçamentos de privacidade acompanham o valor epsilon dos relatórios individuais, bem como o agregado para todos os relatórios. Depois de gastoou esgotado um orçamento de privacidade, os utilizadores já não podem aceder a dados.  

## <a name="reliability-of-data"></a>Fiabilidade dos dados

Embora a preservação da privacidade deva ser o objetivo, há uma compensação no que diz respeito à usabilidade e fiabilidade dos dados. Na análise de dados, a precisão pode ser considerada como uma medida de incerteza introduzida por erros de amostragem. Esta incerteza tende a cair dentro de certos limites. **A precisão** numa perspetiva de privacidade diferencial mede a fiabilidade dos dados, que são afetados pela incerteza introduzida pelos mecanismos de privacidade. Em suma, um nível mais elevado de ruído ou privacidade traduz-se em dados que têm uma epsilon mais baixa, precisão e fiabilidade. Embora os dados sejam mais privados, porque não são fiáveis, menor é a probabilidade de serem usados.

## <a name="implementing-differentially-private-systems"></a>Implementação de sistemas diferenciais privados

A implementação de sistemas diferenciais privados é difícil. WhiteNoise é um projeto de código aberto que contém diferentes componentes para a construção de sistemas privados diferenciais globais. O WhiteNoise é composto por componentes de alto nível:

- Principal
- Sistema

### <a name="core"></a>Principal

A biblioteca principal inclui os seguintes mecanismos de privacidade para a implementação de um sistema diferencial privado:

|Componente  |Descrição  |
|---------|---------|
|Análise     | Uma descrição de gráfico de cálculos arbitrários. |
|Validador     | Uma biblioteca Rust que contém um conjunto de ferramentas para verificar e obter as condições necessárias para que uma análise seja diferencialmente privada.          |
|Runtime     | O meio para executar a análise. O tempo de referência é escrito em Rust, mas os tempos de execução podem ser escritos usando qualquer quadro de computação, como SQL e Spark, dependendo das suas necessidades de dados.        |
|Enlaces     | Encadernações linguísticas e bibliotecas auxiliares para construir análises. Atualmente o WhiteNoise fornece encadernações Python. |

### <a name="system"></a>Sistema

A biblioteca do sistema fornece as seguintes ferramentas e serviços para trabalhar com dados tabulares e relacionais:

|Componente  |Descrição  |
|---------|---------|
|Acesso a Dados     | Biblioteca que interceta e processa consultas SQL e produz relatórios. Esta biblioteca é implementada em Python e suporta as seguintes fontes de dados ODBC e DBAPI:<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Rio Preston</li><li>Pandas</li></ul>|
|Serviço     | Serviço de execução que fornece um ponto final REST para servir pedidos ou consultas contra fontes de dados partilhadas. O serviço foi concebido para permitir a composição de módulos de privacidade diferenciais que operam em pedidos que contenham diferentes valores delta e epsilon, também conhecidos como pedidos heterogéneos. Esta implementação de referência explica o impacto adicional das consultas em dados correlacionados. |
|Avaliador     | Avaliador estocástico que verifica violações de privacidade, precisão e preconceito. O avaliador suporta os seguintes testes: <ul><li>Privacy Test - Determina se um relatório adere às condições de privacidade diferencial.</li><li>Teste de precisão - Mede se a fiabilidade dos relatórios se enquadra nos limites superior e inferior, dado um nível de confiança de 95%.</li><li>Utility Test - Determina se os limites de confiança de um relatório estão suficientemente próximos dos dados, maximizando ainda a privacidade.</li><li>Teste de Enviesamento - Mede a distribuição de relatórios para consultas repetidas para garantir que não são desequilibrados</li></ul> |

## <a name="next-steps"></a>Passos seguintes

Para aprender a usar os componentes do WhiteNoise, consulte os repositórios GitHub para o [pacote WhiteNoise Core,](https://github.com/opendifferentialprivacy/whitenoise-core) [pacote WhiteNoise System](https://github.com/opendifferentialprivacy/whitenoise-system) e [amostras WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-samples).