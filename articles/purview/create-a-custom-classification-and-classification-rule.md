---
title: Criar uma regra de classificação e classificação personalizada (pré-visualização)
description: Saiba como criar classificações personalizadas para definir tipos de dados na sua propriedade de dados que são exclusivos da sua organização em Azure Purview.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/5/2021
ms.openlocfilehash: 2966618619aa40ed60c2f3d0fb2c8e080d34a016
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617051"
---
# <a name="custom-classifications-in-azure-purview"></a>Classificações personalizadas em Azure Purview

Este artigo descreve como pode criar classificações personalizadas para definir tipos de dados no seu espólio de dados que são exclusivos da sua organização. Também descreve a criação de regras de classificação personalizadas que lhe permitem encontrar dados especificados em toda a sua propriedade de dados.

## <a name="default-classifications"></a>Classificações padrão

O Azure Purview Data Catalog fornece um grande conjunto de classificações padrão que representam tipos típicos de dados pessoais que você pode ter no seu espólio de dados.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="selecionar classificação" border="true":::

Você também tem a capacidade de criar classificações personalizadas, se alguma das classificações padrão não atender às suas necessidades.

## <a name="steps-to-create-a-custom-classification"></a>Passos para criar uma classificação personalizada

Para criar uma classificação personalizada, faça o seguinte:

1. No seu catálogo, selecione **Management Center** a partir do menu esquerdo.

2. Selecione **Classificações** sob **gestão de metadados.**

3. Selecione **+ Novo**

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Nova classificação" border="true":::

O **painel de classificação Add abre,** onde pode dar à sua classificação um nome e uma descrição. É uma boa prática usar uma convenção de espaçamento de nomes, `your company name.classification name` como.

As classificações do sistema Microsoft são agrupadas no espaço reservado para o `MICROSOFT.` nome. Um exemplo é **a MICROSOFT. GOVERNO. PARA NÓS. \_ \_ NÚMERO DE SEGURANÇA SOCIAL.**

O nome da sua classificação deve começar com uma letra seguida de uma sequência de letras, números e período (.) ou sublinhar caracteres. Não são permitidos espaços. À medida que escreve, o UX gera automaticamente um nome amigável. Este nome amigável é o que os utilizadores vêem quando o aplicam a um ativo no catálogo.

Para manter o nome curto, o sistema cria o nome amigável com base na seguinte lógica:

- Todos, menos os dois últimos segmentos do espaço de nomes são aparados.

- O invólucro é ajustado de modo a que a primeira letra de cada palavra seja capitalizada. Todas as outras letras são convertidas em minúsculas.

- Todos os sublinhados \_ são substituídos por espaços.

Como exemplo, se nomeou a sua classificação **CONTOSO.HR. Identificação \_ do funcionário,** o nome amigável é armazenado no sistema como **Identificação hr.empregado**.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

Selecione **OK**, e a sua nova classificação é adicionada à sua lista de classificação.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Classificação personalizada" border="true":::

A seleção da classificação na lista abre a página de detalhes da classificação. Aqui, você encontra todos os detalhes sobre a classificação.

Estes detalhes incluem a contagem de quantas ocorrências existem, o nome formal, regras de classificação associadas (se houver), e o nome do proprietário.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Selecione classificação" border="true":::

## <a name="custom-classification-rules"></a>Regras de classificação personalizadas

O serviço de catálogo fornece um conjunto de regras de classificação predefinidos, que são usadas pelo scanner para detetar automaticamente certos tipos de dados. Também pode adicionar as suas próprias regras de classificação personalizadas para detetar outros tipos de dados que poderá estar interessado em encontrar através do seu espólio de dados. Esta capacidade pode ser muito poderosa quando \' está a tentar encontrar dados dentro do seu espólio de dados.

Como exemplo, digamos \' que uma empresa chamada Contoso tem IDs de empregados que são padronizados em toda a empresa com a palavra Empregado seguido por um GUID para criar \" \" EMPLOYEE{GUID}. Por exemplo, um exemplo de identificação de um empregado `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55` parece.

Contoso pode configurar o sistema de digitalização para encontrar instâncias destes IDs criando uma regra de classificação personalizada. Podem fornecer uma expressão regular que corresponda ao padrão de dados, neste caso, `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . Opcionalmente, se os dados estiverem normalmente numa coluna da qual sabem o nome, como ID do Empregado \_ ou EmployeeID, podem adicionar uma expressão regular do padrão de coluna para tornar a digitalização ainda mais precisa. Um exemplo regex é O \_ \| Funcionário ID EmployeeID.

O sistema de digitalização pode então usar esta regra para examinar os dados reais na coluna e o nome da coluna para tentar identificar todos os casos de onde o padrão de identificação do funcionário é encontrado.

## <a name="steps-to-create-a-custom-classification-rule"></a>Passos para criar uma regra de classificação personalizada

Para criar uma regra de classificação personalizada:

1. Crie uma classificação personalizada seguindo as instruções na secção anterior. Irá adicionar esta classificação personalizada na configuração da regra de classificação para que o sistema o aplique quando encontrar uma correspondência na coluna.

2. Selecione o ícone **do Centro de Gestão.**

3. Selecione a secção **de regras classificações.**

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="Azulejo das regras de classificação" border="true":::

4. Selecione **Nova**.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="Adicionar nova regra de classificação" border="true":::

5. Abre-se a nova caixa de diálogo **de regras de classificação.** Preencha os campos e decida se cria uma **regra de expressão regular** ou uma regra do **dicionário**.

   |Campo     |Descrição  |
   |---------|---------|
   |Nome   |    Obrigatório. O máximo é de 100 caracteres.    |
   |Description      |Opcional. O máximo é de 256 caracteres.    |
   |Nome de classificação    | Obrigatório. Selecione o nome da classificação da lista de drop-down para dizer ao scanner para aplicá-lo se for encontrada uma correspondência.        |
   |Estado   |  Obrigatório. As opções estão ativadas ou desativadas. Ativado é o padrão.    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="Criar nova regra de classificação" border="true":::

### <a name="creating-a-regular-expression-rule"></a>Criação de uma regra de expressão regular

1. Se criar uma regra de expressão regular, verá o seguinte ecrã. Pode enviar opcionalmente um ficheiro que será utilizado para **gerar padrões de regundo sugeridos** para a sua regra.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="Criar nova regra regex" border="true":::

1. Se decidir gerar um padrão regex sugerido, depois de carregar um ficheiro, selecione um dos padrões sugeridos e clique em **Adicionar a Padrões para** usar os padrões de dados e colunas sugeridos. Pode ajustar os padrões sugeridos ou também escrever os seus próprios padrões sem carregar um ficheiro.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="Gerar regex sugerido" border="true":::

   |Campo     |Descrição  |
   |---------|---------|
   |Padrão de dados    |Opcional. Uma expressão regular que representa os dados armazenados no campo de dados. O limite é muito grande. No exemplo anterior, os padrões de dados testam para um ID de funcionário que é literalmente a palavra `Employee{GUID}` .  |
   |Padrão da coluna    |Opcional. Uma expressão regular que representa os nomes das colunas que pretende igualar. O limite é muito grande. |

1. De acordo com **o Padrão de Dados,** existem dois limiares que pode definir:

   - **Limiar de correspondência distinto**: O número total de valores de dados distintos que precisam de ser encontrados numa coluna antes que o scanner executa o padrão de dados nele. O valor sugerido é 8. Este valor pode ser ajustado manualmente num intervalo de 2 a 32. O sistema requer este valor para se certificar de que a coluna contém dados suficientes para que o scanner o classifique com precisão. Por exemplo, uma coluna que contenha várias linhas que contenham o valor 1 não será classificada. Colunas que contêm uma linha com um valor e o resto das linhas têm valores nulos também não serão classificados. Se especificar vários padrões, este valor aplica-se a cada um deles.

   - **Limiar mínimo de correspondência**: Pode utilizar esta definição para definir a percentagem mínima das correspondências de valor de dados distintos numa coluna que deve ser encontrada pelo scanner para a classificação a aplicar. O valor sugerido é de 60%. Tens de ter cuidado com esta definição. Se reduzir o nível abaixo dos 60%, poderá introduzir classificações falsamente positivas no seu catálogo. Se especificar vários padrões de dados, esta definição é desativada e o valor é fixado em 60%.

1. Pode agora verificar a  sua regra e criá-la.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="Verifique a regra antes de criar" border="true":::

### <a name="creating-a-dictionary-rule"></a>Criar uma regra do dicionário

1. Se criar uma regra do dicionário, verá o seguinte ecrã. Faça upload de um ficheiro que contenha todos os valores possíveis para a classificação que está a criar numa única coluna.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="Criar regra do dicionário" border="true":::

1. Após a geração do dicionário, pode ajustar os limiares de correspondência e de correspondência mínimos e submeter a regra.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Regra do dicionário de visão - ajuste o limiar de correspondência distinto e o limiar mínimo de correspondência" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Crie a regra do dicionário, com a marca de verificação gerada pelo dicionário." border="true":::

## <a name="next-steps"></a>Passos seguintes

Agora que criou a sua regra de classificação, está pronto para ser adicionado a uma regra de digitalização definida para que a sua digitalização utilize a regra ao digitalizar. Para obter mais informações, consulte [Criar um conjunto de regras de digitalização](create-a-scan-rule-set.md).
