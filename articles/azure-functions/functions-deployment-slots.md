---
title: Azure Functions slots de implantação
description: Aprenda a criar e usar slots de implantação com Azure Functions
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funções
ms.service: azure-functions
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 23a4870332266ce180c2e94aeb0b5ca24073878b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576324"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions slots de implantação

Azure Functions slots de implantação permitem que seu aplicativo de funções execute diferentes instâncias chamadas "Slots". Os slots são ambientes diferentes expostos por meio de um ponto de extremidade disponível publicamente. Uma instância de aplicativo sempre é mapeada para o slot de produção e você pode trocar instâncias atribuídas a um slot sob demanda. Os aplicativos de funções em execução no plano de serviço de aplicativos podem ter vários slots, enquanto em consumo apenas um slot é permitido.

O seguinte reflete como as funções são afetadas por slots de permuta:

- O redirecionamento de tráfego é contínuo; nenhuma solicitação foi descartada devido a uma troca.
- Se uma função estiver em execução durante uma permuta, a execução continuará e os gatilhos subsequentes serão roteados para a instância do aplicativo trocado.

> [!NOTE]
> No momento, os slots não estão disponíveis para o plano de consumo do Linux.

## <a name="why-use-slots"></a>Por que usar Slots?

Há várias vantagens em usar slots de implantação. Os cenários a seguir descrevem usos comuns para Slots:

- **Ambientes diferentes para finalidades diferentes**: usar Slots diferentes oferece a oportunidade de diferenciar instâncias de aplicativo antes de alternar para a produção ou para um slot de preparo.
- **Preaquecimento**: a implantação em um slot em vez de diretamente na produção permite que o aplicativo fique quente antes de entrar no ar. Além disso, o uso de Slots reduz a latência para cargas de trabalho disparadas por HTTP. As instâncias são ativadas antes da implantação, o que reduz a inicialização a frio para funções implantadas recentemente.
- **Fallbacks fáceis**: após uma troca com produção, o slot com um aplicativo previamente preparado agora tem o aplicativo de produção anterior. Se as alterações trocadas no slot de produção não forem as esperadas, você poderá reverter a troca imediatamente para obter a "última instância boa conhecida" de volta.

## <a name="swap-operations"></a>Operações de permuta

Durante uma troca, um slot é considerado a origem e o outro destino. O slot de origem tem a instância do aplicativo que é aplicada ao slot de destino. As etapas a seguir garantem que o slot de destino não experimente tempo de inatividade durante uma troca:

1. **Aplicar configurações:** As configurações do slot de destino são aplicadas a todas as instâncias do slot de origem. Por exemplo, as configurações de produção são aplicadas à instância de preparo. As configurações aplicadas incluem as seguintes categorias:
    - Configurações do aplicativo e cadeias [de conexão específicas do slot](#manage-settings) (se aplicável)
    - Configurações de [implantação contínua](../app-service/deploy-continuous-deployment.md) (se habilitada)
    - Configurações de [autenticação do serviço de aplicativo](../app-service/overview-authentication-authorization.md) (se habilitado)

1. **Aguarde as reinicializações e a disponibilidade:** A permuta espera que cada instância no slot de origem conclua sua reinicialização e esteja disponível para solicitações. Se alguma instância não for reiniciada, a operação de permuta reverterá todas as alterações no slot de origem e interromperá a operação.

1. **Roteamento de atualização:** Se todas as instâncias no slot de origem forem ativadas com êxito, os dois slots concluirão a permuta alternando as regras de roteamento. Após essa etapa, o slot de destino (por exemplo, o slot de produção) tem o aplicativo que foi anteriormente ativado no slot de origem.

1. **Repetir operação:** Agora que o slot de origem tem o aplicativo de pré-permuta anteriormente no slot de destino, execute a mesma operação aplicando todas as configurações e reiniciando as instâncias do slot de origem.

Tenha em consideração os seguintes pontos:

- Em qualquer ponto da operação de permuta, a inicialização dos aplicativos trocados ocorre no slot de origem. O slot de destino permanece online enquanto o slot de origem está sendo preparado, se a troca é bem-sucedida ou falha.

- Para trocar um slot de preparo pelo slot de produção, verifique se o slot de produção é *sempre* o slot de destino. Dessa forma, a operação de permuta não afeta seu aplicativo de produção.

- As configurações relacionadas a origens e associações de eventos precisam ser definidas como [configurações de slot de implantação](#manage-settings) *antes de iniciar uma troca*. Marcá-los como "adesivos" antecipadamente garante que os eventos e as saídas sejam direcionados para a instância apropriada.

## <a name="manage-settings"></a>Gerir definições

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Criar uma configuração de implantação

Você pode marcar configurações como uma configuração de implantação que o torna "adesivo". Uma configuração adesiva não alterna com a instância do aplicativo.

Se você criar uma configuração de implantação em um slot, certifique-se de criar a mesma configuração com um valor exclusivo em qualquer outro slot envolvido em uma troca. Dessa forma, embora o valor de uma configuração não mude, os nomes de configuração permanecem consistentes entre os slots. Essa consistência de nome garante que seu código não tente acessar uma configuração definida em um slot, mas não em outra.

Use as etapas a seguir para criar uma configuração de implantação:

- Navegue até os *Slots* no aplicativo de funções
- Clique no nome do slot
- Em *recursos da plataforma > configurações gerais*, clique em **configuração**
- Clique no nome da configuração que você deseja colocar com o slot atual
- Clique na caixa de seleção **configuração do slot de implantação**
- Clique em **OK**
- Após a definição da folha desaparecer, clique em **salvar** para manter as alterações

![Configuração do slot de implantação](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Implementação

Os slots ficam vazios quando você cria um slot. Você pode usar qualquer uma das [tecnologias de implantação com suporte](./functions-deployment-technologies.md) para implantar seu aplicativo em um slot.

## <a name="scaling"></a>Dimensionamento

Todos os slots são dimensionados para o mesmo número de trabalhadores que o slot de produção.

- Para planos de consumo, o slot é dimensionado conforme o aplicativo de funções é dimensionado.
- Para planos do serviço de aplicativo, o aplicativo é dimensionado para um número fixo de trabalhadores. Os slots são executados no mesmo número de trabalhadores que o plano do aplicativo.

## <a name="add-a-slot"></a>Adicionar um slot

Você pode adicionar um slot por meio da [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) ou por meio do Portal. As etapas a seguir demonstram como criar um novo slot no Portal:

1. Navegue até seu aplicativo de funções e clique no **sinal de adição** ao lado de *Slots*.

    ![Adicionar Azure Functions slot de implantação](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Insira um nome na caixa de texto e pressione o botão **criar** .

    ![Nome Azure Functions slot de implantação](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Slots de permuta

Você pode trocar os slots por meio da [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) ou por meio do Portal. As etapas a seguir demonstram como trocar slots no Portal:

1. Navegue até o aplicativo de funções
1. Clique no nome do slot de origem que você deseja alternar
1. Na guia *visão geral* , clique no botão **alternar** ![slot de implantação de Azure Functions de permuta](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Verifique os parâmetros de configuração para sua permuta e clique em **trocar** ![trocar Azure Functions slot de implantação](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

A operação pode demorar um pouco enquanto a operação de permuta está em execução.

## <a name="roll-back-a-swap"></a>Reverter uma permuta

Se uma troca resultar em um erro ou se você simplesmente quiser "desfazer" uma troca, poderá reverter para o estado inicial. Para retornar ao estado de pré-atualização, faça outra troca para reverter a troca.

## <a name="remove-a-slot"></a>Remover um slot

Você pode remover um slot por meio da [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) ou por meio do Portal. As etapas a seguir demonstram como remover um slot no Portal:

1. Navegue até o aplicativo de funções visão geral

1. Clique no botão **excluir**

    ![Adicionar Azure Functions slot de implantação](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatizar o gerenciamento de Slots

Usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest), você pode automatizar as seguintes ações para um slot:

- [criar](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [eliminar](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [permuta](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [troca automática](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Alterar plano do serviço de aplicativo

Com um aplicativo de funções que está sendo executado em um plano do serviço de aplicativo, você tem a opção de alterar o plano do serviço de aplicativo subjacente para um slot.

> [!NOTE]
> Não é possível alterar o plano do serviço de aplicativo de um slot no plano de consumo.

Use as etapas a seguir para alterar o plano do serviço de aplicativo de um slot:

1. Navegar até um slot

1. Em *recursos da plataforma*, clique em **todas as configurações**

    ![Alterar plano do serviço de aplicativo](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Clique no **plano do serviço de aplicativo**

1. Selecione um novo plano do serviço de aplicativo ou crie um novo plano

1. Clique em **OK**

    ![Alterar plano do serviço de aplicativo](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Limitações

Azure Functions slots de implantação têm as seguintes limitações:

- O número de slots disponíveis para um aplicativo depende do plano. O plano de consumo só é permitido para um slot de implantação. Slots adicionais estão disponíveis para aplicativos em execução no plano do serviço de aplicativo.
- A troca de um slot redefine chaves para aplicativos que têm uma configuração de aplicativo `AzureWebJobsSecretStorageType` igual a `files`.
- Os slots não estão disponíveis para o plano de consumo do Linux.

## <a name="support-levels"></a>Níveis de suporte

Há dois níveis de suporte para slots de implantação:

- **Disponibilidade geral (GA)** : suporte completo e aprovado para uso em produção.
- Versão **prévia**: ainda não tem suporte, mas é esperado para alcançar o status de GA no futuro.

| Sistema operacional/plano de hospedagem           | Nível de suporte     |
| ------------------------- | -------------------- |
| Consumo do Windows       | Disponibilidade geral |
| Windows Premium           | Disponibilidade geral  |
| Windows dedicado         | Disponibilidade geral |
| Consumo do Linux         | Não suportado          |
| Linux Premium             | Disponibilidade geral  |
| Linux dedicado           | Disponibilidade geral |

## <a name="next-steps"></a>Passos seguintes

- [Tecnologias de implantação no Azure Functions](./functions-deployment-technologies.md)
