---
title: Implementar amostra de projeto da Fundação de Benchmark de Segurança Azure
description: Implementar etapas para a amostra de projeto da Azure Security Benchmark Foundation, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 02/17/2020
ms.topic: sample
ms.openlocfilehash: aaaabc8767c6d80548a26d64d8557587180fb6f3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095293"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Implementar a amostra de projeto da Fundação de Referência de Segurança Azure

Para implantar a amostra de projeto da Fundação Azure Security Benchmark Foundation, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de projeto da **Fundação Azure Security Benchmark** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de projeto da Azure Security Benchmark Foundation.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos que compõem o esquema de exemplo. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do projeto da Fundação de Benchmark de Segurança Azure.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de mudança** como "Primeira versão publicada a partir da amostra de projeto da Azure Security Benchmark Foundation." Em seguida, selecione **Publicar**, na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas
       - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar.
     - O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído.
       Para saber mais, consulte [identidades geridas para recursos Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Escolha a opção de identidade gerida _atribuída pelo sistema_ predefinida ou a opção de identidade _atribuída pelo utilizador_.

   - Parâmetros de esquema

     Os parâmetros definidos nesta secção são utilizados por muitos dos artefactos na definição do esquema, para proporcionar consistência.
    
     - **Prefixo para recursos e grupos de recursos**: Esta cadeia é usada como um prefixo para todos os nomes de grupos de recursos e recursos
     - **Nome do hub**: Nome para o centro
     - **Retenção de registos (dias)**: Número de dias em que os registos são mantidos; entrar em '0' mantém registos indefinidamente
     - **Hub de implantação**: Introduza 'verdadeiro' ou 'falso' para especificar se a atribuição implementa os componentes do hub da arquitetura
     - **Localização do hub**: Localização para o grupo de recursos do hub
     - **Endereços IP destino**: Endereços IP de destino para conectividade de saída; lista separada por vírgula de endereços IP ou prefixos de gama IP
     - **Nome do Observador de Rede**: Nome do recurso Network Watcher
     - **Nome do grupo de recursos do Network Watcher**: Nome para o grupo de recursos Do Observador de Rede
     - **Ativar a proteção DDoS**: Introduza 'verdadeiro' ou 'falso' para especificar se a Proteção DDoS está ou não ativada na rede virtual
     
    > [!NOTE] 
    > Se o Network Watcher já estiver ativado, recomenda-se que utilize o grupo de recursos do Observador de Rede existente. Também deve fornecer a localização do grupo de recursos do Observador de Rede existente para a localização do **grupo de recursos do observador de imagens do** parâmetro do artefacto .

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela a seguir fornece uma lista dos parâmetros da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Grupo de recursos hub|Grupo de recursos|Nome do grupo de recursos|Bloqueado - Concatenates prefixo com nome do hub|
|Grupo de recursos hub|Grupo de recursos|Localização do grupo de recursos|Bloqueado - Usa a localização do hub|
|Modelo de firewall Azure|Modelo do Resource Manager|Endereço IP privado Azure Firewall||
|Modelo de Azure Log Analytics e Diagnóstico|Modelo do Resource Manager|Log Analytics local do espaço de trabalho|Localização onde o espaço de trabalho Log Analytics é criado; executar `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` em Azure PowersShell para ver regiões disponíveis|
|Modelo de Azure Log Analytics e Diagnóstico|Modelo do Resource Manager|ID da conta Azure Automation (opcional)|ID de recursos de conta de automação; usado para criar um serviço ligado entre Log Analytics e uma conta de Automação|
|Modelo do grupo de segurança da rede Azure|Modelo do Resource Manager|Ativar registos de fluxo NSG|Introduza 'verdadeiro' ou 'falso' para ativar ou desativar registos de fluxo NSG|
|Modelo de hub de rede virtual Azure|Modelo do Resource Manager|Prefixo de endereço de rede virtual|Prefixo de endereço de rede virtual para rede virtual do hub|
|Modelo de hub de rede virtual Azure|Modelo do Resource Manager|Prefixo do endereço do endereço da sub-rede de firewall|Prefixo de endereço de sub-rede de firewall para rede virtual do hub|
|Modelo de hub de rede virtual Azure|Modelo do Resource Manager|Prefixo do endereço da sub-rede de bastião|Prefixo de endereço de sub-rede de bastião para rede virtual do hub|
|Modelo de hub de rede virtual Azure|Modelo do Resource Manager|Prefixo do endereço da sub-rede gateway|Prefixo de endereço de sub-rede gateway para rede virtual do hub|
|Modelo de hub de rede virtual Azure|Modelo do Resource Manager|Prefixo do endereço de sub-rede de gestão|Prefixo de endereço de sub-rede de gestão para rede virtual do hub|
|Modelo de hub de rede virtual Azure|Modelo do Resource Manager|Prefixo do endereço do endereço da caixa de salto|Prefixo de endereço de sub-rede de caixa de salto para rede virtual do hub|
|Modelo de hub de rede virtual Azure|Modelo do Resource Manager|Nomes de endereços de sub-rede (opcional)|Matriz de nomes de sub-redes para implantar na rede virtual do hub; por exemplo, "subnet1", "subnet2"|
|Modelo de hub de rede virtual Azure|Modelo do Resource Manager|Prefixos de endereço de sub-rede (opcional)|Matriz de prefixos de endereço IP para sub-redes opcionais para rede virtual hub; por exemplo, "10.0.7.0/24","10.0.8.0/24"|
|Grupo de recursos falado|Grupo de recursos|Nome do grupo de recursos|Bloqueado - Concatenates prefixo com nome de falado|
|Grupo de recursos falado|Grupo de recursos|Localização do grupo de recursos|Bloqueado - Usa a localização do hub|
|Modelo de fala de rede virtual Azure|Modelo do Resource Manager|Implementar falou|Insira 'verdadeiro' ou 'falso' para especificar se a atribuição implementa os componentes falados da arquitetura|
|Modelo de fala de rede virtual Azure|Modelo do Resource Manager|ID de assinatura do hub|ID de assinatura onde o hub é implantado; valor padrão é a subscrição onde a definição de planta está localizada|
|Modelo de fala de rede virtual Azure|Modelo do Resource Manager|Nome de falado|Nome do falador|
|Modelo de fala de rede virtual Azure|Modelo do Resource Manager|Prefixo de endereço de rede virtual|Prefixo de endereço de rede virtual para rede virtual falada|
|Modelo de fala de rede virtual Azure|Modelo do Resource Manager|Prefixo do endereço do endereço da sub-rede|Prefixo de endereço de sub-rede para rede virtual falada|
|Modelo de fala de rede virtual Azure|Modelo do Resource Manager|Nomes de endereços de sub-rede (opcional)|Matriz de nomes de sub-redes para implantar na rede virtual falada; por exemplo, "subnet1", "subnet2"|
|Modelo de fala de rede virtual Azure|Modelo do Resource Manager|Prefixos de endereço de sub-rede (opcional)|Matriz de prefixos de endereço IP para sub-redes opcionais para a rede virtual falada; por exemplo, "10.0.7.0/24","10.0.8.0/24"|
|Modelo de fala de rede virtual Azure|Modelo do Resource Manager|Implementar falou|Insira 'verdadeiro' ou 'falso' para especificar se a atribuição implementa os componentes falados da arquitetura|
|Modelo de observador de rede Azure|Modelo do Resource Manager|Localização do Observador de Rede|Localização para o recurso Network Watcher|
|Modelo de observador de rede Azure|Modelo do Resource Manager|Localização do grupo de recursos do Observador de Rede|Se o Network Watcher já estiver ativado, este valor de parâmetro **deve** corresponder ao nome do grupo de recursos do Observador de Rede existente.|

## <a name="troubleshooting"></a>Resolução de problemas

Se encontrar o `The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.` erro, verifique se o nome do **grupo de recursos do Network Watcher** especifica o nome do grupo de recursos do Observador de Rede existente e que a localização do grupo de recursos do Observador de **rede** de artefactos especifica a localização do grupo de recursos do Observador de Rede existente.

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de projeto da Fundação Azure Security Benchmark, visite o seguinte artigo para saber sobre a arquitetura:

> [!div class="nextstepaction"]
> [Azure Security Benchmark Foundation blueprint - Visão geral](./index.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
