---
title: Normalização de dados em Azure Sentinel | Microsoft Docs
description: Este artigo explica como o Azure Sentinel normaliza dados de várias fontes diferentes e detalha o esquema de normalização.
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
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 5d847ac7ed805ad88bc24ed63896edc6f7596f9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729781"
---
# <a name="normalization-in-azure-sentinel"></a>Normalização em Azure Sentinel

Este artigo explica a normalização do esquema de dados no Azure Sentinel, e especificamente nas ligações de rede e esquema de sessões, para o qual está incluído um link.

## <a name="what-is-normalization"></a>O que é normalização

Trabalhar em conjunto com vários tipos de dados e tabelas apresenta desafios. Deve familiarizar-se com muitos tipos e esquemas de dados diferentes, tendo de escrever e utilizar um conjunto único de regras de análise, livros de trabalho e consultas de caça para cada um, mesmo para aqueles que partilham comunhões (por exemplo, relativos a dispositivos de firewall). A correlação entre os diferentes tipos de dados, necessária para a investigação e a caça, também é difícil. O Azure Sentinel proporciona uma experiência perfeita para lidar com dados de várias fontes em vistas uniformes e normalizadas.

O **modelo de informação comum** Azure Sentinel consiste em três aspetos:

- **Os esquemas normalizados cobrem conjuntos comuns** de tipos de eventos previsíveis (tabelas) que são fáceis de trabalhar e para construir capacidades unificadas em (por exemplo, mesa de networking). O esquema também inclui uma convenção de colunas normalizada, e definições para a normalização de valor e formato (representação padrão consistente de dados como endereços IP).

- **Os parsers mapeiam** os dados existentes de diferentes tipos para o esquema normalizado. De acordo com o modelo, os dados podem ser analisados ao esquema normalizado no tempo de consulta (usando funções) ou tempo de ingestão. Neste momento, apenas a análise do tempo de consulta é suportada.

- **O conteúdo de cada esquema normalizado** inclui regras de análise, livros interativos, consultas de caça e conteúdo adicional.

### <a name="current-release"></a>Lançamento atual

Com esta versão, as [ligações de rede normalizadas e o esquema de sessões](./normalization-schema.md) (v1.0.0) estão disponíveis para visualização pública. O esquema descreve ligações de rede ou sessões como as registadas por firewalls, Wire Data, NSG, Netflow, sistemas de procuração e gateways de segurança web.  Os parsers de tempo de consulta selecionados e as regras de análise estão disponíveis juntamente com o esquema, e fazem uso do mesmo.

O esquema está atualmente disponível apenas através de parsers de tempo de consulta (ver secção de parsers).

Pode analisar dados para representações adicionais e utilizar o [modelo de nomeação de entidades OSSEM](https://ossemproject.com/cdm/entities/intro.html#) para criar colunas que serão consistentes com as tabelas normalizadas existentes e futuras.

## <a name="normalized-schema-and-parsing"></a>Esquema e análise normalizados

### <a name="how-our-normalized-schemas-are-defined"></a>Como os nossos esquemas normalizados são definidos

O Azure Sentinel está a alinhar-se com o modelo de informação comum de Eventos de [Segurança de Código Aberto (OSSEM),](https://ossemproject.com/intro.html) permitindo uma correlação previsível de entidades entre tabelas normalizadas. OSSEM é um projeto liderado pela comunidade que se foca principalmente na documentação e normalização de registos de eventos de segurança de diversas fontes de dados e sistemas operativos. Além disso, o projeto fornece um modelo de informação comum (CIM) que pode ser usado para engenheiros de dados durante os procedimentos de normalização de dados para permitir que os analistas de segurança consultam e analisem dados através de diversas fontes de dados.

A [OSSEM CIM](https://ossemproject.com/cdm/intro.html) define um conjunto de entidades (por exemplo: arquivo, anfitrião, IP, processo), e define um conjunto de atributos para cada uma dessas entidades. Além disso, a CIM define um conjunto de tabelas (por exemplo, tabela de sessão de [rede)](https://ossemproject.com/cdm/tables/network_session.html) que utilizam atributos relevantes destas entidades, permitindo uma correlação perfeita e previsível. Note que as entidades podem ser aninhadas (por exemplo, a entidade fonte pode conter uma entidade de Arquivo que terá um atributo de nome).

Para saber mais sobre a estrutura da entidade OSSEM, visite a referência oficial da [OSSEM.](https://ossemproject.com/cdm/guidelines/entity_structure.html)

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Como os esquemas normalizados são implementados em Azure Sentinel

Na implementação da CIM OSSEM em Azure Sentinel, estamos projetando a representação da OSSEM para uma representação tabular Log Analytics, quer esta representação seja uma tabela incorporada ou foi criada usando parsers ou funções de tempo de consulta que mapeiam os dados existentes para esta representação. Para a representação tabular, estamos a concatenar nomes de entidades OSSEM e a atribuir nomes, e mapeando-os coletivamente para um nome de uma única coluna. Por exemplo, uma entidade Source que contenha uma entidade de Ficheiros contendo uma entidade Hash contendo um atributo md5, será implementada como a seguinte coluna Log Analytics: SrcFileHashMd5. (OSSEM utiliza *snake_case* por defeito, enquanto Azure Sentinel e Log Analytics usam *PascalCase*. Em OSSEM tal coluna seria src_file_hash_md5.)

Podem existir campos personalizados adicionais na implementação do Azure Sentinel, devido aos requisitos da plataforma Log Analytics e aos casos de utilização específicos dos clientes do Azure Sentinel.

### <a name="schema-reference"></a>Referência de esquema

Consulte o [documento de referência do esquema,](./normalization-schema.md)bem como a documentação oficial do projeto [OSSEM,](https://ossemproject.com/cdm/intro.html)para saber mais.

A referência de esquema também inclui padronização de valor e formato. Os campos de origem, originais ou analisados, podem não estar num formato normalizado ou utilizar a lista padrão de valores do esquema, pelo que teriam de ser convertidos para a representação padrão do esquema, a fim de serem totalmente normalizados.

## <a name="parsers"></a>Parsers

- [O que está a analisar](#what-is-parsing)
- [Usando os parsers do tempo de consulta](#using-query-time-parsers)

### <a name="what-is-parsing"></a>O que está a analisar

Com um conjunto base de tabelas normalizadas definidas disponíveis, terá de transformar (parse/map) os seus dados nessas tabelas. Ou seja, irá extrair dados específicos da sua forma bruta em colunas bem conhecidas no esquema normalizado. A análise em Azure Sentinel acontece na **hora da consulta** - os parsers são construídos como funções de utilizador do Log Analytics (usando a Linguagem de Consulta de Kusto - KQL) que transformam dados em tabelas existentes (como CommonSecurityLog, tabelas de registos personalizados, syslog) no esquema de tabelas normalizadas.

O outro tipo de análise, ainda não suportado no Azure Sentinel, está no momento de **ingestão** - permitindo recolher dados diretamente na(s) tabela(s) normalizada à medida que é ingerida a partir das suas fontes de dados. A análise do tempo de ingestão proporciona um melhor desempenho, uma vez que o modelo de dados é consultado diretamente sem a necessidade de usar funções.

### <a name="using-query-time-parsers"></a>Usando os parsers do tempo de consulta

- [Instalação de um parser](#installing-a-parser)
- [Usando os parsers](#using-the-parsers)
- [Personalizar parsers](#customizing-parsers)

#### <a name="installing-a-parser"></a>Instalação de um parser

Os parsers de tempo de consulta disponíveis estão disponíveis no [repositório oficial do GitHub do](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))Azure Sentinel. Cada parser é versão para permitir que os clientes utilizem e monitorizem facilmente para futuras atualizações. Para instalar um analisador:

1. Copie o conteúdo relevante do parser de cada ficheiro KQL relevante na ligação GitHub acima para a sua área de transferência

1. No portal Azure Sentinel, abra a página 'Logs' e cole o conteúdo do ficheiro KQL no ecrã de registos e clique em **Guardar**.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Instale um novo parser":::

1. No diálogo de salvamento, povoe os campos da seguinte forma:
    1. **Denominação**: Recomenda-se a utilização do mesmo valor utilizado no campo **Alias de Função** (no exemplo acima, *CheckPoint_Network_NormalizedParser*)
    
    1. **Guardar como**: selecione **Função**

    1. **Função Alias**: Deve ser nomeado na seguinte convenção de nomeação - *PRODUCT_Network_NormalizedParser* (no exemplo acima, *CheckPoint_Network_NormalizedParser*).

    1. **Categoria**: pode selecionar uma categoria existente ou criar uma nova categoria (como *NormalizedNetworkSessionsParsers)*
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="Salve o parser":::

Para utilizar corretamente os parsers, deve também instalar o parser de esquemas de rede Vazio (que cria uma visão tabular vazia de todos os campos de esquemas de sessões de rede) e o meta-parser da rede (que junta todos os parsers habilitados a criar uma única visão de dados de várias fontes no esquema de rede). A instalação destes dois parsers é feita de forma semelhante às etapas acima mencionadas.

Ao guardar uma função de consulta, pode ser necessário fechar o explorador de consulta e reabri-lo para que a nova função seja refletida.

#### <a name="using-the-parsers"></a>Usando os parsers

Uma vez ativado, pode utilizar o meta-parser para consultar uma visão unificada de todos os parsers atualmente ativados. Para tal, vá à página de registos do Azure Sentinel e questione o meta-parser:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Consulta o parser":::
 
Também pode aceder ao meta-parser ou aos parsers individuais utilizando o explorador de consultas na página de registos, clicando em 'Explorador de consulta':

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Explorador de consultas":::

No painel à direita, expanda a secção "Consultas Guardadas" e encontre a pasta 'NormalizaçãoNetworkParsers' (ou o nome da categoria que escolheu ao criar os parsers):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Encontre o seu parser":::

Pode clicar em cada analisador individual e ver a função subjacente que utiliza, e executá-la (ou aceder-lhe diretamente pelo seu pseudónimo, conforme descrito acima). Note que alguns parsers podem reter os campos originais lado a lado para os campos normalizados por conveniência. Isto pode ser facilmente editado na consulta do parser.

> [!TIP]
> Pode utilizar as suas funções guardadas em vez de tabelas Azure Sentinel em qualquer consulta, incluindo consultas de caça e deteção. Para obter mais informações, consulte:
>
> - [Normalização de dados em Azure Sentinel](normalization.md#parsers)
> - [Texto de parse em registos do Monitor Azure](../azure-monitor/logs/parse-text.md)
>
#### <a name="customizing-parsers"></a>Personalizar parsers

Pode repetir os passos acima (encontrar o parser no explorador de consultas), clicar no parser relevante e ver a sua implementação da função.
Por exemplo, pode decidir editar o meta-parser para adicionar/remover os parsers individuais.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Personalize o seu parser":::
 
Uma vez alterada a função, clique em "Guardar" novamente e use o mesmo nome, pseudónimo e categoria. Será aberto um diálogo de sobreposição – prima "OK":

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="Tens a certeza":::

#### <a name="additional-information"></a>Informações adicionais

JSON, XML e CSV são especialmente convenientes para a análise na hora da consulta. O Azure Sentinel tem funções de análise incorporadas para JSON, XML e CSV, bem como uma ferramenta de análise JSON.  Para obter mais informações, consulte [os campos JSON em Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) (blog). 

Saiba mais sobre [consultas guardadas](../azure-monitor/logs/example-queries.md) (a implementação dos parsers de consulta) no Log Analytics.


## <a name="next-steps"></a>Passos seguintes

Neste documento, soube do esquema de normalização de Azure Sentinel. Para o esquema de referência em si, consulte [a referência de esquema de normalização de dados do Azure Sentinel](./normalization-schema.md).

* [Azure Sentinel Blog](https://aka.ms/azuresentinelblog). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.