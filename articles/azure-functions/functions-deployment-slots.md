---
title: Ranhuras de implantação de funções azure
description: Aprenda a criar e utilizar ranhuras de implantação com funções Azure
author: craigshoemaker
ms.topic: reference
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 7cfbd533921ba4d1757e7415a3bb8f70aeb71251
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122600"
---
# <a name="azure-functions-deployment-slots"></a>Ranhuras de implantação de funções azure

As ranhuras de implementação das Funções Azure permitem que a sua aplicação de funções execute diferentes instâncias chamadas "slots". As ranhuras são diferentes ambientes expostos através de um ponto final disponível publicamente. Uma instância de aplicação está sempre mapeada para a ranhura de produção, e você pode trocar instâncias atribuídas a uma ranhura a pedido. As aplicações de função que executam o plano de Serviço de Apps podem ter várias faixas horárias, enquanto no âmbito do plano de Consumo apenas é permitida uma ranhura.

Os seguintes refletem a forma como as funções são afetadas pela troca de faixas horárias:

- A reorientação do tráfego é perfeita; nenhum pedido é retirado por causa de uma troca.
- Se uma função estiver em execução durante uma troca, a execução continua e os próximos gatilhos são encaminhados para a instância de aplicação trocada.

> [!NOTE]
> Não estão atualmente disponíveis slots para o plano de consumo linux.

## <a name="why-use-slots"></a>Por que usar slots?

Há uma série de vantagens em usar ranhuras de implantação. Os seguintes cenários descrevem utilizações comuns para slots:

- **Ambientes diferentes para diferentes fins**: A utilização de diferentes slots dá-lhe a oportunidade de diferenciar as instâncias das aplicações antes de trocar para produção ou uma ranhura de encenação.
- **Pré-aquecimento**: A implantação para uma ranhura em vez de diretamente para a produção permite que a app aqueça antes de entrar em direto. Além disso, a utilização de faixas horárias reduz a latência para cargas de trabalho desencadeadas por HTTP. As instâncias são aquecidas antes da implantação, o que reduz o arranque a frio para funções recém-implantadas.
- **Recuos fáceis**: Depois de uma troca com a produção, a ranhura com uma aplicação previamente encenada tem agora a app de produção anterior. Se as alterações trocadas na ranhura de produção não forem como espera, pode imediatamente inverter a troca para recuperar a sua "última boa instância conhecida".

## <a name="swap-operations"></a>Operações de troca

Durante uma troca, uma ranhura é considerada a fonte e a outra o alvo. A ranhura de origem tem a instância da aplicação que é aplicada à ranhura-alvo. Os seguintes passos garantem que a ranhura-alvo não experimenta tempo de inatividade durante uma troca:

1. **Aplicar as definições:** As definições da ranhura-alvo são aplicadas em todas as instâncias da ranhura de origem. Por exemplo, as definições de produção são aplicadas na instância de encenação. As definições aplicadas incluem as seguintes categorias:
    - Definições de aplicativos [específicos para slot](#manage-settings) e cordas de ligação (se aplicável)
    - Definições [de implementação contínuas](../app-service/deploy-continuous-deployment.md) (se ativadas)
    - [Definições de autenticação do Serviço de Aplicações](../app-service/overview-authentication-authorization.md) (se ativadas)

1. **Aguarde o reinício e disponibilidade:** A permuta aguarda que cada instância na ranhura de origem complete o seu reinício e esteja disponível para pedidos. Se alguma instância não reiniciar, a operação de permuta reverte todas as alterações à ranhura de origem e para a operação.

1. **Atualização de encaminhamento:** Se todas as instâncias na ranhura de origem forem aquecidas com sucesso, as duas ranhuras completam a permuta trocando as regras de encaminhamento. Após este passo, a ranhura-alvo (por exemplo, a ranhura de produção) tem a app que já foi aquecida na ranhura de origem.

1. **Operação de repetição:** Agora que a ranhura de origem tem a aplicação de pré-permuta anteriormente na ranhura-alvo, completa a mesma operação aplicando todas as definições e reiniciando as instâncias para a ranhura de origem.

Tenha em consideração os seguintes pontos:

- Em qualquer ponto da operação de permuta, a inicialização das aplicações trocadas ocorre na ranhura de origem. A ranhura-alvo permanece on-line enquanto a ranhura de origem está preparada, quer a permuta tenha sucesso ou falhe.

- Para trocar uma ranhura de preparação com a ranhura de produção, certifique-se de que a ranhura de produção é *sempre* a ranhura-alvo. Desta forma, a operação de swap não afeta a sua aplicação de produção.

- As definições relacionadas com fontes de eventos e encadernações devem ser configuradas como definições de ranhura de [implantação](#manage-settings) *antes de iniciar uma troca*. A marcação como "pegajosa" antes do tempo garante que os eventos e saídas são direcionados para a instância adequada.

## <a name="manage-settings"></a>Gerir definições

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Criar uma definição de implementação

Pode marcar as definições como uma definição de implantação, o que o torna "pegajoso". Uma definição pegajosa não troca com a instância da aplicação.

Se criar uma definição de implementação numa ranhura, certifique-se de criar a mesma definição com um valor único em qualquer outra ranhura que esteja envolvida numa troca. Desta forma, embora o valor de uma definição não mude, os nomes de definição permanecem consistentes entre as ranhuras. Esta consistência de nome garante que o seu código não tenta aceder a uma definição definida numa ranhura, mas não noutra.

Utilize os seguintes passos para criar uma definição de implantação:

1. Navegue para **as ranhuras de implementação** na aplicação de função e, em seguida, selecione o nome da ranhura.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Encontre vagas no portal Azure." border="true":::

1. **Selecione Configuração**e, em seguida, selecione o nome de definição que pretende ficar com a ranhura atual.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Configure a definição de aplicação para uma ranhura no portal Azure." border="true":::

1. Selecione **a definição**de ranhura de implantação e, em seguida, selecione **OK**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Configure a definição de ranhura de implantação." border="true":::

1. Uma vez que a secção de regulação desapareça, selecione **Guardar** para manter as alterações

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Guarde a definição de ranhura de implantação." border="true":::

## <a name="deployment"></a>Implementação

As ranhuras estão vazias quando se cria uma ranhura. Pode utilizar qualquer uma das tecnologias de [implementação suportadas](./functions-deployment-technologies.md) para implementar a sua aplicação numa ranhura.

## <a name="scaling"></a>Dimensionamento

Todas as faixas horárias são dimensionadas para o mesmo número de trabalhadores que a ranhura de produção.

- Para os planos de consumo, a ranhura escala à medida que a aplicação de função escala.
- Para os planos do Serviço de Aplicações, a aplicação dimensiona para um número fixo de trabalhadores. As faixas horárias funcionam no mesmo número de trabalhadores que o plano de aplicações.

## <a name="add-a-slot"></a>Adicionar um bloco

Pode adicionar uma ranhura através do [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) ou através do portal. Os seguintes passos demonstram como criar uma nova ranhura no portal:

1. Navegue para a sua aplicação de funções.

1. Selecione ranhuras de **implantação,** e, em seguida, selecione **+ Adicionar ranhura**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Adicione o slot de implantação das Funções Azure." border="true":::

1. Digite o nome da ranhura e selecione **Adicionar**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Nomeie a ranhura de implantação das Funções Azure." border="true":::

## <a name="swap-slots"></a>Trocar vagas

Pode trocar slots através do [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) ou através do portal. Os seguintes passos demonstram como trocar faixas horárias no portal:

1. Navegue para a aplicação de funções.
1. Selecione ranhuras de **implantação,** e, em seguida, selecione **Swap**.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Troque a ranhura de implantação." border="true":::

1. Verifique as definições de configuração para o seu swap e selecione **Swap**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Troque a ranhura de implantação." border="true":::

A operação pode demorar um momento enquanto a operação de troca está a ser executada.

## <a name="roll-back-a-swap"></a>Recue uma troca

Se uma troca resultar num erro ou se simplesmente quiser "desfazer" uma troca, pode voltar ao estado inicial. Para voltar ao estado pré-trocado, faça outra troca para inverter a troca.

## <a name="remove-a-slot"></a>Remova uma ranhura

Pode remover uma ranhura através do [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) ou através do portal. Os seguintes passos demonstram como remover uma ranhura no portal:

1. Navegue para **as ranhuras de implementação** na aplicação de função e, em seguida, selecione o nome da ranhura.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Encontre vagas no portal Azure." border="true":::

1. Selecione **Eliminar**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Elimine a ranhura de implantação no portal Azure." border="true":::

1. Digite o nome da ranhura de implantação que pretende eliminar e, em seguida, **selecione Eliminar**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Elimine a ranhura de implantação no portal Azure." border="true":::

1. Feche o painel de confirmação de exclusão.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Ranhura de implantação eliminar confirmação." border="true":::

## <a name="automate-slot-management"></a>Gestão de slot automatizada

Utilizando o [Azure CLI,](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)pode automatizar as seguintes ações para uma ranhura:

- [criar](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [excluir](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [troca](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [auto-swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Alterar plano de serviço de aplicações

Com uma aplicação de função que está a ser realizada ao abrigo de um plano de Serviço de Aplicações, pode alterar o plano de Serviço de Aplicações subjacente para uma vaga.

> [!NOTE]
> Não pode alterar o plano de serviço de aplicações de uma ranhura no âmbito do plano de consumo.

Utilize os seguintes passos para alterar o plano de serviço de aplicações de uma ranhura:

1. Navegue para **as ranhuras de implementação** na aplicação de função e, em seguida, selecione o nome da ranhura.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Encontre vagas no portal Azure." border="true":::

1. No **plano do Serviço de Aplicações,** selecione plano de serviço de **aplicações de mudança**.

1. Selecione o plano para o quais pretende atualizar ou crie um novo plano.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Altere o plano de Serviço de Aplicações no portal Azure." border="true":::

1. Selecione **OK**.

## <a name="limitations"></a>Limitações

As ranhuras de implantação das Funções Azure têm as seguintes limitações:

- O número de vagas disponíveis para uma aplicação depende do plano. O plano de consumo só é permitido uma vaga de implantação. Estão disponíveis slots adicionais para aplicações que executam o plano de Serviço de Aplicações.
- Trocar uma ranhura repõe as teclas para apps que tenham uma `AzureWebJobsSecretStorageType` definição de aplicação igual a `files` .
- Não estão disponíveis slots para o plano de consumo linux.

## <a name="support-levels"></a>Níveis de apoio

Existem dois níveis de suporte para as faixas horárias de implantação:

- **Disponibilidade geral (GA)**: Totalmente suportado e aprovado para utilização da produção.
- **Pré-visualização**: Ainda não suportado, mas espera-se que atinja o estatuto de GA no futuro.

| Plano de Alojamento/HOSPEDEIRO           | Nível de apoio     |
| ------------------------- | -------------------- |
| Consumo de janelas       | Disponibilidade geral |
| Windows Premium           | Disponibilidade geral  |
| Windows Dedicado         | Disponibilidade geral |
| Consumo de Linux         | Não suportado          |
| Prémio Linux             | Disponibilidade geral  |
| Linux Dedicado           | Disponibilidade geral |

## <a name="next-steps"></a>Passos seguintes

- [Tecnologias de implantação em Funções Azure](./functions-deployment-technologies.md)
