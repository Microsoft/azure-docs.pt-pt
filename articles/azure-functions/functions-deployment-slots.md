---
title: Slots de implementação de funções Azure
description: Aprenda a criar e utilizar slots de implementação com funções Azure
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 73282145abd8bfe804b47fda3bf5f12dc691ff3a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551361"
---
# <a name="azure-functions-deployment-slots"></a>Slots de implementação de funções Azure

As ranhuras de implementação de Funções Azure permitem que a sua aplicação de função execute diferentes instâncias chamadas "slots". As ranhuras são diferentes ambientes expostos através de um ponto final disponível ao público. Uma instância de aplicação é sempre mapeada para a ranhura de produção, e você pode trocar instâncias atribuídas a uma ranhura a pedido. As aplicações de função que executam ao abrigo do plano de Serviço de Aplicações podem ter várias faixas horárias, enquanto no âmbito do plano De Consumo apenas é permitida uma ranhura.

As seguintes funções são afetadas pela troca de faixas horárias:

- A reorientação do tráfego é perfeita; nenhum pedido é retirado por causa de uma troca.
- Se uma função estiver a ser executada durante uma troca, a execução continua e os próximos gatilhos são encaminhados para a instância da aplicação trocada.

## <a name="why-use-slots"></a>Por que usar slots?

Há uma série de vantagens na utilização de slots de implantação. Os seguintes cenários descrevem utilizações comuns para slots:

- **Ambientes diferentes para diferentes finalidades**: A utilização de diferentes slots dá-lhe a oportunidade de diferenciar as instâncias da aplicação antes de trocar para a produção ou uma ranhura de preparação.
- **Pré-guerra**: Implantar para uma ranhura em vez de diretamente para a produção permite que a app aqueça antes de entrar em direto. Além disso, a utilização de slots reduz a latência para cargas de trabalho desencadeadas por HTTP. As ocorrências são aquecidas antes da implantação, o que reduz o arranque a frio para as funções recém-implantadas.
- **Recuos fáceis**: Após uma troca com a produção, a ranhura com uma aplicação previamente encenada tem agora a aplicação de produção anterior. Se as alterações trocadas na ranhura de produção não forem como se espera, pode reverter imediatamente a troca para recuperar a sua "última boa instância conhecida".

## <a name="swap-operations"></a>Operações de troca

Durante uma troca, uma ranhura é considerada a fonte e a outra o alvo. A ranhura de origem tem a instância da aplicação que é aplicada à ranhura do alvo. Os seguintes passos garantem que a ranhura do alvo não experimenta tempo de inatividade durante uma troca:

1. **Aplicar definições:** As definições da ranhura-alvo são aplicadas a todas as instâncias da ranhura de origem. Por exemplo, as definições de produção são aplicadas à fase de paragem. As definições aplicadas incluem as seguintes categorias:
    - [Definições de](#manage-settings) aplicativos específicas de slot e cadeias de conexão (se aplicável)
    - [Definições de implementação contínua](../app-service/deploy-continuous-deployment.md) (se ativadas)
    - [Definições de autenticação do Serviço de Aplicações](../app-service/overview-authentication-authorization.md) (se ativadas)

1. **Aguarde o recomeço e disponibilidade:** O swap aguarda que cada instância na ranhura de origem complete o seu reinício e esteja disponível para pedidos. Se alguma instância não reiniciar, a operação de troca reverte todas as alterações na ranhura de origem e interrompe o funcionamento.

1. **Encaminhamento de atualização:** Se todas as instâncias da ranhura de origem forem aquecidas com sucesso, as duas ranhuras completam a troca com as regras de encaminhamento. Após este passo, a ranhura-alvo (por exemplo, a ranhura de produção) tem a aplicação que já foi previamente aquecida na ranhura de origem.

1. **Repetição da operação:** Agora que a ranhura de origem possui a aplicação de pré-troca anteriormente na ranhura alvo, complete a mesma operação aplicando todas as definições e reiniciando as instâncias para a ranhura de origem.

Tenha em consideração os seguintes pontos:

- Em qualquer ponto da operação de troca, a inicialização das aplicações trocadas ocorre na ranhura de origem. A ranhura do alvo permanece on-line enquanto a ranhura de origem é preparada, se a troca tem sucesso ou falha.

- Para trocar uma ranhura de encenação com a ranhura de produção, certifique-se de que a ranhura de produção é *sempre* a ranhura-alvo. Desta forma, a operação de troca não afeta a sua aplicação de produção.

- As definições relacionadas com as fontes de eventos e as encadernações devem ser configuradas como [definições de slot de implantação](#manage-settings) *antes de iniciar uma troca*. Marcá-los como "pegajosos" antes do tempo garante que os eventos e saídas são direcionados para o caso adequado.

## <a name="manage-settings"></a>Gerir definições

Algumas definições de configuração são específicas da ranhura. As seguintes listas detalham quais as definições que mudam quando troca slots e que permanecem as mesmas.

**Definições específicas de slot:**

* Pontos finais de publicação
* Nomes de domínio personalizados
* Certificados não públicos e definições de TLS/SSL
* Configurações de escala
* Agendadores WebJobs
* Restrições ip
* Sempre ligado
* Definições de diagnóstico
* Partilha de recursos transversais à origem (CORS)

**Definições não específicas de slot:**

* Configurações gerais, tais como versão-quadro, tomadas web de 32/64 bits
* Definições de aplicativos (pode ser configurado para manter uma ranhura)
* Cordas de ligação (podem ser configuradas para se colarem a uma ranhura)
* Mapeamentos de manipulador
* Certificados públicos
* Conteúdo da WebJobs
* Ligações híbridas *
* Integração de rede virtual *
* Pontos finais de serviço *
* Rede de Entrega de Conteúdos Azure *

As características marcadas com um asterisco (*) estão planeadas para não seremgrafadas. 

> [!NOTE]
> Algumas definições de aplicações que se aplicam a definições não mapeadas também não são trocadas. Por exemplo, uma vez que as definições de diagnóstico não são trocadas, as definições de aplicações relacionadas como `WEBSITE_HTTPLOGGING_RETENTION_DAYS` e também não são `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` trocadas, mesmo que não apareçam como configurações de slot.
>

### <a name="create-a-deployment-setting"></a>Criar uma definição de implementação

Pode marcar as definições como uma definição de implantação, o que o torna "pegajoso". Uma definição pegajosa não se troca com a instância da aplicação.

Se criar uma definição de implementação numa ranhura, certifique-se de criar a mesma definição com um valor único em qualquer outra ranhura que esteja envolvida numa troca. Desta forma, embora o valor de uma definição não se altere, os nomes de definição permanecem consistentes entre slots. Esta consistência de nome garante que o seu código não tenta aceder a uma definição que é definida numa ranhura, mas não noutra.

Utilize os seguintes passos para criar uma definição de implantação:

1. Navegue para **slots de implementação** na aplicação de função e, em seguida, selecione o nome da ranhura.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Encontre slots no portal Azure." border="true":::

1. Selecione **Configuração** e, em seguida, selecione o nome de definição que pretende manter-se na ranhura atual.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Configurar a definição de aplicação para uma ranhura no portal Azure." border="true":::

1. Selecione **a definição de ranhura de implementação** e, em seguida, selecione **OK**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Configure a definição da ranhura de colocação." border="true":::

1. Uma vez que a secção de definição desaparece, **selecione Guardar** para manter as alterações

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Guarde a definição da ranhura de implantação." border="true":::

## <a name="deployment"></a>Implementação

As ranhuras estão vazias quando se cria uma ranhura. Pode utilizar qualquer uma das tecnologias de [implantação suportadas](./functions-deployment-technologies.md) para implementar a sua aplicação numa ranhura.

## <a name="scaling"></a>Dimensionamento

Todas as faixas horárias escalam para o mesmo número de trabalhadores que a ranhura de produção.

- Para os planos de consumo, as escalas de slot como a tabela de aplicações de função.
- Para os planos do Serviço de Aplicações, a aplicação escala para um número fixo de trabalhadores. As faixas horárias funcionam com o mesmo número de trabalhadores que o plano de aplicações.

## <a name="add-a-slot"></a>Adicionar um bloco

Pode adicionar uma ranhura através do [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create) ou através do portal. Os seguintes passos demonstram como criar uma nova ranhura no portal:

1. Navegue para a sua aplicação de função.

1. Selecione **slots de implementação** e, em seguida, selecione **+ Adicionar ranhura**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Adicione a ranhura de implementação de Funções Azure." border="true":::

1. Digite o nome da ranhura e selecione **Adicionar**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Nomeie a ranhura de implantação de Funções Azure." border="true":::

## <a name="swap-slots"></a>Trocar slots

Pode trocar slots através do [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap) ou através do portal. Os seguintes passos demonstram como trocar slots no portal:

1. Navegue para a aplicação de função.
1. Selecione **slots de implementação** e, em seguida, selecione **Swap**.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Screenshot que mostra a página 'Slot', com a ação 'Add Slot' selecionada." border="true":::

1. Verifique as definições de configuração para a sua troca e selecione **Swap**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Troque a ranhura de implantação." border="true":::

A operação pode demorar um momento enquanto a operação de troca está a ser executada.

## <a name="roll-back-a-swap"></a>Reverta uma troca

Se um swap resultar num erro ou se simplesmente quiser "desfazer" uma troca, pode voltar ao estado inicial. Para voltar ao estado pré-trocado, faça outra troca para inverter a troca.

## <a name="remove-a-slot"></a>Remover uma ranhura

Pode remover uma ranhura através do [CLI](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete) ou através do portal. Os seguintes passos demonstram como remover uma ranhura no portal:

1. Navegue para **slots de implementação** na aplicação de função e, em seguida, selecione o nome da ranhura.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Encontre slots no portal Azure." border="true":::

1. Selecione **Eliminar**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Screenshot que mostra a página 'Visão Geral' com a ação 'Delete' selecionada." border="true":::

1. Digite o nome da ranhura de implantação que pretende eliminar e, em seguida, **selecione Delete**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Elimine a ranhura de implantação no portal Azure." border="true":::

1. Feche o painel de confirmação de eliminação.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="A abertura da ranhura elimina a confirmação." border="true":::

## <a name="automate-slot-management"></a>Automatizar a gestão de slots

Utilizando o [Azure CLI,](/cli/azure/functionapp/deployment/slot)pode automatizar as seguintes ações para uma ranhura:

- [criar](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create)
- [eliminar](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete)
- [lista](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-list)
- [troca](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap)
- [troca automática](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Alterar plano de Serviço de Aplicações

Com uma aplicação de função que está a decorrer ao abrigo de um plano de Serviço de Aplicações, pode alterar o plano de Serviço de Aplicações subjacente para uma ranhura.

> [!NOTE]
> Não é possível alterar o plano de Serviço de Aplicações de uma ranhura ao abrigo do plano de consumo.

Utilize os seguintes passos para alterar o plano de Serviço de Aplicações de uma ranhura:

1. Navegue para **slots de implementação** na aplicação de função e, em seguida, selecione o nome da ranhura.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Encontre slots no portal Azure." border="true":::

1. No âmbito **do plano de Serviço de Aplicações,** selecione o plano de Serviço de **Aplicações de Alteração.**

1. Selecione o plano para o quais pretende atualizar ou criar um novo plano.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Alterar o plano de Serviço de Aplicações no portal Azure." border="true":::

1. Selecione **OK**.

## <a name="limitations"></a>Limitações

As ranhuras de implementação de funções Azure têm as seguintes limitações:

- O número de faixas horárias disponíveis para uma aplicação depende do plano. O plano de consumo só é permitido uma ranhura de implantação. Slots adicionais estão disponíveis para aplicações em execução ao abrigo do plano de Serviço de Aplicações.
- Trocar uma ranhura repõe as teclas para apps que tenham uma `AzureWebJobsSecretStorageType` definição de aplicação igual a `files` .
- As vagas não estão disponíveis para o plano de consumo de Linux.

## <a name="support-levels"></a>Níveis de suporte

Existem dois níveis de suporte para slots de implantação:

- **Disponibilidade geral (GA)**: Totalmente suportado e aprovado para utilização de produção.
- **Pré-visualização**: Ainda não suportado, mas espera-se que atinja o estatuto de GA no futuro.

| Plano de hospedagem OS/Hospedagem           | Nível de suporte     |
| ------------------------- | -------------------- |
| Consumo de Janelas       | Disponibilidade geral |
| Windows Premium           | Disponibilidade geral  |
| Windows Dedicado         | Disponibilidade geral |
| Consumo de Linux         | Pré-visualizar          |
| Linux Premium             | Disponibilidade geral  |
| Linux Dedicado           | Disponibilidade geral |

## <a name="next-steps"></a>Passos seguintes

- [Tecnologias de implantação em Funções Azure](./functions-deployment-technologies.md)
