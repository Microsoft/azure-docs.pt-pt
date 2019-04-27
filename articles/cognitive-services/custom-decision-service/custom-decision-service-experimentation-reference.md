---
title: Experimentação - serviço de decisão personalizada
titlesuffix: Azure Cognitive Services
description: Este artigo é um guia para a experimentação com o serviço de decisão personalizada.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b5f8c853218a1db53f4dd23e7254b35990a7132b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829179"
---
# <a name="experimentation"></a>Experimentação

Seguindo a teoria das [bandits contextuais (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), serviço de decisão personalizada repetidamente observa um contexto, efetua uma ação e observa uma recompensa para a ação escolhida. Um exemplo é a personalização de conteúdo: o contexto descreve um utilizador, as ações são histórias de Release candidate e a recompensa mede o usuário quanto gostou a história recomendada.

Serviço de decisão personalizada produz uma política, como ele mapeia de contextos de ações. Com uma política de destino específica, quer saber sua recompensa esperada. Uma forma de estimar a recompensa é utilizar uma política online e deixá-lo a escolher ações (por exemplo, recomendamos que histórias aos utilizadores). No entanto, essa avaliação online pode ser dispendiosa por dois motivos:

* Ele expõe os utilizadores a uma política não testado, experimental.
* Ele não pode ser escalonada para avaliar várias diretivas de destino.

Avaliação-policy é uma paradigma alternativa. Se tiver registos a partir de um sistema online existente que se seguem uma política de registo, avaliação-policy pode estimar as recompensas esperadas de novas políticas de destino.

Ao utilizar o ficheiro de registo, a experimentação procura localizar a política com a recompensa estimada, esperada mais alta. Políticas de destino são parametrizadas por [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) argumentos. No modo padrão, o script testa uma variedade de argumentos de Vowpal Wabbit acrescentando-se para o `--base_command`. O script realiza as seguintes ações:

* Deteta automaticamente apresenta os espaços de nomes do primeiro `--auto_lines` linhas do ficheiro de entrada.
* Efetua uma primeira abertura através de hiper parâmetros (`learning rate`, `L1 regularization`, e `power_t`).
* Testes de avaliação de políticas `--cb_type` (pontuação propensão inversa (`ips`) ou duplamente robusto (`dr`). Para obter mais informações, consulte [exemplo de Bandit contextuais](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Marginals de testes.
* Recursos de interação quadrática de testes:
   * **fase de força bruta**: Testes de todas as combinações com `--q_bruteforce_terms` pares ou menos.
   * **fase greedy**: Adiciona o par de melhor até que não haja nenhum aperfeiçoamento para `--q_greedy_stop` Arredonda por excesso.
* Efetua um segunda paramétrico através de hiper parâmetros (`learning rate`, `L1 regularization`, e `power_t`).

Os parâmetros que controlam essas etapas incluem alguns argumentos Vowpal Wabbit:
- Opções de manipulação de exemplo:
  - espaços de nomes partilhados
  - espaços de nomes de ação
  - espaços de nomes marginais
  - funcionalidades quadrática
- Opções de atualização de regra
  - taxa de aprendizagem
  - L1 regularização
  - valor de energia de t

Para obter uma explicação detalhada dos argumentos acima, consulte [argumentos de linha de comandos Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Pré-requisitos
- Vowpal Wabbit: Instalado e no seu caminho.
  - Windows: [Utilize o `.msi` instalador](https://github.com/eisber/vowpal_wabbit/releases).
  - Outras plataformas: [Obter o código-fonte](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Instalado e no seu caminho.
- NumPy: Utilize o Gestor de pacotes da sua preferência.
- O *mwt/Microsoft-ds* repositório: [Clone o repositório](https://github.com/Microsoft/mwt-ds).
- Ficheiro de registo de JSON do serviço de decisão: Por predefinição, o comando de base inclui `--dsjson`, que permite a análise de JSON do serviço de decisão do arquivo de dados de entrada. [Obter um exemplo desse formato](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Utilização
Aceda a `mwt-ds/DataScience` e execute `Experimentation.py` com os argumentos relevantes, conforme detalhado no código a seguir:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Um registo dos resultados é acrescentado para o *mwt-ds/DataScience/experiments.csv* ficheiro.

### <a name="parameters"></a>Parâmetros
| Input | Descrição | Predefinição |
| --- | --- | --- |
| `-h`, `--help` | Mostre mensagem de ajuda e de saída. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Caminho do ficheiro de dados (`.json` ou `.json.gz` formato - cada linha é um `dsjson`). | Necessário |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Comando de Vowpal Wabbit base.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Número de processos paralelos. | Processadores lógicos |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Partilhado espaços de nomes do recurso (por exemplo, `abc` significa que os espaços de nomes `a`, `b`, e `c`).  | Detetar automaticamente a partir do ficheiro de dados |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Ação funcionalidade espaços de nomes. | Detetar automaticamente a partir do ficheiro de dados |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Espaços de nomes de poucos recursos. | Detetar automaticamente a partir do ficheiro de dados |  
| `--auto_lines AUTO_LINES` | Número de linhas do ficheiro de dados para análise para detetar automaticamente espaços de nomes de recursos. | `100` |  
| `--only_hp` | Apenas através de hiper parâmetros de abertura (`learning rate`, `L1 regularization`, e `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Intervalo de taxa de aprendizagem como valores positivos `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Intervalo de regularização de L1 como valores positivos `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Intervalo de Power_t como valores positivos `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Número de pares quadrática para testar na fase de força bruta. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Arredonda sem aprimoramentos, após o qual a fase de pesquisa greedy quadrática é suspensa. | `3` |  

### <a name="examples"></a>Exemplos
Para utilizar os valores predefinidos predefinidas:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

De maneira equivalente, também pode ingerir o Vowpal Wabbit `.json.gz` ficheiros:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

A abertura apenas através de hiper parâmetros (`learning rate`, `L1 regularization`, e `power_t`, a parar após o passo 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
