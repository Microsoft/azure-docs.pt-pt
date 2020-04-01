---
title: Tutorial de controlo si& de aplicações - Azure Security Center
description: Este tutorial mostra-lhe como configurar uma política de acesso VM just-in-time e uma política de controlo de aplicações.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 0b28de7af16053093cd0108224188cdd615fce55
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435510"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Tutorial: proteger os seus recursos com o Centro de Segurança do Azure
O Centro de Segurança limita a exposição a ameaças ao utilizar controlos de acesso e aplicações para bloquear atividade maliciosa. O acesso da máquina virtual (VM) just-in-time (VM) reduz a sua exposição a ataques, permitindo-lhe negar o acesso persistente aos VMs. Em alternativa, o utilizador fornece acesso controlado e auditado a VMs apenas quando necessário. Os controlos de aplicações adaptáveis ajudam a proteger as VMs contra software maligno ao controlar as aplicações que podem ser executadas nas suas VMs. O Centro de Segurança utiliza machine learning para analisar os processos que estão a ser executados na VM e ajuda-o a aplicar regras de inclusão nas listas de permissões com base nessas informações.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure uma política de acesso VM just-in-time
> * Configurar uma política de controlo de aplicações

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar as funcionalidades abrangidas neste tutorial, tem de estar no escalão de preço Standard do Centro de Segurança. Pode tentar o Nível Padrão do Centro de Segurança sem custos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Incluir a sua subscrição do Azure no Centro de Segurança Standard) explica-lhe como atualizar para Standard.

## <a name="manage-vm-access"></a>Gerir o acesso à VM
O acesso ao VM JIT pode ser utilizado para bloquear o tráfego de entrada para os seus VMs Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário.

As portas de gestão não precisam de estar abertas permanentemente. Apenas têm de estar abertas enquanto estiver ligado à VM, por exemplo, para realizar tarefas de gestão ou manutenção. Quando o tempo é ativado, o Security Center utiliza as regras do Network Security Group (NSG), que restringem o acesso a portas de gestão para que não possam ser alvo de atacantes.

1. No menu principal do Security Center, selecione **acesso VM just-in-time** sob **DEFESA DE NUVEM AVANÇADA**.

   ![Acesso just-in-time à VM][1]

   **O acesso vm just-in-time** fornece informações sobre o estado dos seus VMs:

   - **Configurados** - VMs que foram configurados para suportar acesso vM just-in-time.
   - **Recomendado** - VMs que podem suportar acesso vm just-in-time, mas não foram configurados para.
   - **Nenhuma recomendação** - As razões que podem fazer com que uma VM não seja recomendada são:

     - NSG desaparecido - A solução just-in-time requer que um NSG esteja no lugar.
     - O acesso VM clássico do Centro de Segurança just-in-time suporta atualmente apenas VMs implantados através do Azure Resource Manager.
     - Outros - Um VM está nesta categoria se a solução just-in-time for desligada na política de segurança da subscrição ou do grupo de recursos, ou se o VM está faltando um IP público e não tem um NSG no lugar.

2. Selecione um VM recomendado e clique **em Ativar jIT em 1 VM** para configurar uma política justa para esse VM:

   Pode guardar as portas padrão que o Security Center recomenda ou pode adicionar e configurar uma nova porta na qual pretende ativar a solução just-in-time. Neste tutorial, vamos adicionar uma porta ao selecionar **Adicionar**.

   ![Adicionar configuração da porta][2]

3. Em **Adicionar configuração da porta**, identifique:

   - A porta
   - O tipo de protocolo
   - IPs de origem permitidos - os intervalos de IP autorizados a obter acesso após um pedido aprovado
   - Tempo máximo do pedido - janela de tempo máximo em que pode ser aberta uma porta específica

4. Selecione **OK** para guardar.

## <a name="harden-vms-against-malware"></a>Proteger VMs contra software maligno
Os controlos de aplicações adaptáveis ajudam a definir um conjunto de aplicações cuja execução é permitida em grupos de recursos configurados, o que, entre outras vantagens, ajuda a proteger as suas VMs contra software maligno. O Centro de Segurança utiliza machine learning para analisar os processos que estão a ser executados na VM e ajuda-o a aplicar regras de inclusão nas listas de permissões com base nessas informações.

1. Volte ao menu principal do Centro de Segurança. Em **DEFESA DE CLOUD AVANÇADA**, selecione **Controlos de aplicações adaptáveis**.

   ![Controlos de aplicações adaptáveis][3]

   A secção **de grupos de recursos** contém três separadores:

   - **Configurados**: lista de grupos de recursos que contêm as VMs que foram configuradas com o controlo de aplicações.
   - **Recomendados**: lista de grupos de recursos para os quais o controlo de aplicações é recomendado.
   - **Nenhuma recomendação**: lista de grupos de recursos que contêm as VMs para as quais não existem recomendações de controlo de aplicações. Por exemplo, VMs em que as aplicações estão sempre a ser alteradas e que não atingiram um estado estável.

2. Selecione o separador **Recomendados** para ver uma lista dos grupos de recursos com recomendações de controlo de aplicações.

   ![Recomendações de controlo de aplicações][4]

3. Selecione um grupo de recursos para abrir a opção **Criar regras de controlo de aplicações**. Em **Selecionar VMs**, reveja a lista de VMs recomendadas e desmarque aquelas às quais não pretende aplicar o controlo de aplicações. Em **Selecionar processos para regras de inclusão em listas de permissões**, reveja a lista de aplicações recomendadas e desmarque aquelas às quais não pretende aplicar. A lista inclui:

   - **NOME**: o caminho completo da aplicação
   - **PROCESSOS**: quantas aplicações residem em cada caminho
   - **COMMON**: "Sim" indica que estes processos foram executados na maioria dos VMs deste grupo de recursos
   - **EXPLOREABLE**: Um ícone de aviso indica se as aplicações podem ser utilizadas por um intruso para contornar a listagem de aplicações. Recomenda-se que reveja estas aplicações antes da respetiva aprovação.

4. Depois de concluir as suas seleções, selecione **Criar**.

## <a name="clean-up-resources"></a>Limpar recursos
Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se pretender continuar a trabalhar com quickstarts e tutoriais subsequentes, continue a executar o nível Standard e mantenha o fornecimento automático ativado. Se não pretender continuar ou quiser voltar para o Escalão gratuito:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de Segurança**.
2. Selecione a subscrição ou a política para a qual pretende voltar como Gratuita. **Política de segurança** abre-se.
3. Em **COMPONENTES DA POLÍTICA**, selecione **Escalão de preço**.
4. Selecione **Gratuito** para alterar a subscrição, do Escalão standard para o Escalão gratuito.
5. Selecione **Guardar**.

Se pretender desativar aprovisionamento automático:

1. Volte ao menu principal do Centro de Segurança e selecione a política de **segurança.**
2. Selecione a subscrição para a qual pretende desativar o aprovisionamento automático.
3. Em **Política de segurança – Recolha de Dados**, selecione **Desativar** em **Inclusão** para desativar o aprovisionamento automático.
4. Selecione **Guardar**.

>[!NOTE]
> A desativação automática não remove o agente Log Analytics dos VMs Azure onde o agente foi provisionado. Desativar o aprovisionamento automático limita a monitorização da segurança dos seus recursos.
>

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a limitar a exposição a ameaças ao:

> [!div class="checklist"]
> * Configurar uma política de acesso VM just-in-time para fornecer acesso controlado e auditado aos VMs apenas quando necessário
> * Configurar uma política de controlos de aplicações adaptáveis para controlar as aplicações que podem ser executadas nas suas VMs

Avance para o próximo tutorial para aprender a responder a incidentes de segurança.

> [!div class="nextstepaction"]
> [Tutorial: responder a incidentes de segurança](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
