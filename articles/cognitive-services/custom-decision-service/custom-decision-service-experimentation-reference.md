---
title: Serviço de Decisão Personalizada de experimentação
titlesuffix: Azure Cognitive Services
description: Este artigo é um guia para experimentação com Serviço de Decisão Personalizada.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: e6e8e7d0d5b969464ba9183ccae9080f58f786a0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707288"
---
# <a name="experimentation"></a>Experimentação

Após a teoria do [Bandits contextual (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), serviço de decisão personalizada observa repetidamente um contexto, executa uma ação e observa um prêmio para a ação escolhida. Um exemplo é a personalização de conteúdo: o contexto descreve um usuário, as ações são histórias candidatas e a recompensa mede o quanto o usuário gostou da história recomendada.

Serviço de Decisão Personalizada produz uma política, à medida que ela é mapeada de contextos para ações. Com uma política de destino específica, você deseja saber sua recompensa esperada. Uma maneira de estimar a recompensa é usar uma política online e deixá-la escolher ações (por exemplo, as histórias recomendadas para os usuários). No entanto, essa avaliação online pode ser dispendiosa por dois motivos:

* Ele expõe os usuários a uma política experimental não testada.
* Ele não é dimensionado para avaliar várias políticas de destino.

A avaliação fora da política é um paradigma alternativo. Se você tiver logs de um sistema online existente que seguem uma política de registro em log, a avaliação fora da política poderá estimar as recompensas esperadas das novas políticas de destino.

Usando o arquivo de log, a experimentação procura encontrar a política com a recompensa mais alta estimada e esperada. As políticas de destino são parametrizadas por argumentos [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) . No modo padrão, o script testa uma variedade de argumentos Vowpal Wabbit acrescentando ao `--base_command`. O script realiza as seguintes ações:

* Automaticamente detecta namespaces de recursos das primeiras `--auto_lines` linhas do arquivo de entrada.
* Executa uma primeira varredura sobre hiperparâmetros (`learning rate`, `L1 regularization`, e `power_t`).
* Testa a avaliação `--cb_type` da política (Pontuação de propensação inversa (`ips`) ou duplamente robusta (`dr`). Para obter mais informações, consulte [exemplo de Bandit contextual](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testa margens.
* Testa os recursos de interação quadrática:
   * **fase de força bruta**: Testa todas as combinações `--q_bruteforce_terms` com pares ou menos.
   * **fase de ávido**: Adiciona o melhor par até que não haja nenhum aperfeiçoamento para `--q_greedy_stop` arredondas.
* Executa uma segunda varredura sobre hiperparâmetros (`learning rate`, `L1 regularization`, e `power_t`).

Os parâmetros que controlam essas etapas incluem alguns argumentos Vowpal Wabbit:
- Opções de manipulação de exemplo:
  - namespaces compartilhados
  - namespaces de ação
  - namespaces marginais
  - recursos do quadrática
- Atualizar opções de regra
  - taxa de aprendizagem
  - Regularização L1
  - valor de energia t

Para obter uma explicação detalhada dos argumentos acima, consulte argumentos de [linha de comando Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Pré-requisitos
- Wabbit Vowpal: Instalado e em seu caminho.
  - Windows: [Use o `.msi` instalador](https://github.com/eisber/vowpal_wabbit/releases).
  - Outras plataformas: [Obtenha o código-fonte](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Instalado e em seu caminho.
- NumPy Use o Gerenciador de pacotes de sua escolha.
- O repositório *Microsoft/MWT-DS* : [Clone o repositório](https://github.com/Microsoft/mwt-ds).
- Arquivo de log JSON do serviço de decisão: Por padrão, o comando de base `--dsjson`inclui, que habilita a análise JSON do serviço de decisão do arquivo de dados de entrada. [Obtenha um exemplo desse formato](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Utilização
Vá para `mwt-ds/DataScience` e execute `Experimentation.py` com os argumentos relevantes, conforme detalhado no código a seguir:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Um log dos resultados é anexado ao arquivo *MWT-DS/dataciência/experimentos. csv* .

### <a name="parameters"></a>Parâmetros
| Input | Descrição | Predefinição |
| --- | --- | --- |
| `-h`, `--help` | Mostrar mensagem de ajuda e sair. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Caminho do arquivo de`.json` dados `.json.gz` (ou formato-cada linha `dsjson`é um). | Requerido |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Comando base Vowpal Wabbit.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Número de processos paralelos a serem usados. | Processadores lógicos |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Namespaces de recurso compartilhado (por exemplo `abc` , significa `a`namespaces `b`, e `c`).  | Detecção automática de arquivo de dados |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Namespaces de recurso de ação. | Detecção automática de arquivo de dados |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Namespaces de recurso marginal. | Detecção automática de arquivo de dados |  
| `--auto_lines AUTO_LINES` | Número de linhas do arquivo de dados a serem verificadas para detectar automaticamente os namespaces de recursos. | `100` |  
| `--only_hp` | Varredura apenas sobre hiperparâmetros (`learning rate`, `L1 regularization`, e `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Intervalo de taxa de aprendizagem como `min,max,steps`valores positivos. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Intervalo de regularização L1 como valores `min,max,steps`positivos. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Intervalo de Power_t como valores `min,max,step`positivos. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Número de pares de quadrática a serem testados na fase de força bruta. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Arredonda sem melhorias, após o qual a fase de pesquisa de ávidos quadrática é interrompida. | `3` |  

### <a name="examples"></a>Exemplos
Para usar os valores padrão predefinidos:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

De maneira equivalente, Vowpal Wabbit também pode `.json.gz` ingerir arquivos:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Para fazer a varredura somente sobre hiperparâmetros `L1 regularization`(`learning rate`, `power_t`e, parando após a etapa 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
