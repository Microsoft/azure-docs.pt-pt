---
title: Tutorial do Centro de Segurança do Azure – proteger os seus recursos com o Centro de Segurança do Azure | Microsoft Docs
description: Este tutorial mostra como configurar uma política de acesso à VM just-in-time e uma política de controlo de aplicações.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: v-mohabe
ms.openlocfilehash: 7533a8636697db92a69cfec99d0e89138164d7b7
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147890"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Tutorial: Proteja seus recursos com a central de segurança do Azure
O Centro de Segurança limita a exposição a ameaças ao utilizar controlos de acesso e aplicações para bloquear atividade maliciosa. O acesso à VM (just-in-time) de máquina virtual (JIT) reduz sua exposição a ataques, permitindo que você negue o acesso persistente às VMs. Em alternativa, o utilizador fornece acesso controlado e auditado a VMs apenas quando necessário. Os controlos de aplicações adaptáveis ajudam a proteger as VMs contra software maligno ao controlar as aplicações que podem ser executadas nas suas VMs. O Centro de Segurança utiliza machine learning para analisar os processos que estão a ser executados na VM e ajuda-o a aplicar regras de inclusão nas listas de permissões com base nessas informações.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar uma política de acesso à VM just-in-time
> * Configurar uma política de controlo de aplicações

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar as funcionalidades abrangidas neste tutorial, tem de estar no escalão de preço Standard do Centro de Segurança. Você pode experimentar a central de segurança Standard sem nenhum custo. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Incluir a sua subscrição do Azure no Centro de Segurança Standard) explica-lhe como atualizar para Standard.

## <a name="manage-vm-access"></a>Gerir o acesso à VM
O acesso à VM JIT pode ser usado para bloquear o tráfego de entrada para suas VMs do Azure, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário.

As portas de gestão não precisam de estar abertas permanentemente. Apenas têm de estar abertas enquanto estiver ligado à VM, por exemplo, para realizar tarefas de gestão ou manutenção. Quando o acesso just-in-time estiver ativado, o Centro de Segurança utiliza regras do Grupo de Segurança de Rede (NSG), que restringem o acesso às portas de gestão, para que não possam ser visadas pelos atacantes.

1. No menu principal da central de segurança, selecione **acesso à VM just-in-time** em **defesa de nuvem avançada**.

   ![Acesso à VM just-in-time][1]

   O **acesso just-in-time à VM** fornece informações sobre o estado de suas VMs:

   - **Configurado** - VMs que foram configuradas para suportar o acesso à VM just-in-time.
   - **Recomendado** - VMs que conseguem suportar o acesso à VM just-in-time, mas não foram configuradas para tal.
   - **Nenhuma recomendação** - As razões que podem fazer com que uma VM não seja recomendada são:

     - NSG em falta - A solução just-in-time requer a existência de um NSG implementado.
     - VM clássica - Atualmente, o acesso à VM just-in-time do Centro de Segurança suporta apenas VMs implementadas através do Azure Resource Manager.
     - Outro - Uma VM está nesta categoria se a solução just-in-time estiver desativada na política de segurança da subscrição ou do grupo de recursos, ou se existir um IP público em falta na VM e não tiver um NSG implementado.

2. Selecione uma VM recomendada e clique em **Ativar JIT em 1 VM** para configurar uma política just-in-time para essa VM:

   Pode guardar as portas predefinidas recomendadas pelo Centro de Segurança ou pode adicionar e configurar uma porta nova na qual queira ativar a solução just-in-time. Neste tutorial, vamos adicionar uma porta ao selecionar **Adicionar**.

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

   ![Controlos de aplicação adaptável][3]

   A secção **Grupos de recursos** contém três separadores:

   - **Configurado**: Lista de grupos de recursos que contêm as VMs que foram configuradas com o controle de aplicativo.
   - **Recomendado**: Lista de grupos de recursos para os quais o controle de aplicativo é recomendado.
   - **Nenhuma recomendação**: Lista de grupos de recursos que contêm VMs sem nenhuma recomendação de controle de aplicativo. Por exemplo, VMs em que as aplicações estão sempre a ser alteradas e que não atingiram um estado estável.

2. Selecione o separador **Recomendados** para ver uma lista dos grupos de recursos com recomendações de controlo de aplicações.

   ![Recomendações de controlo de aplicações][4]

3. Selecione um grupo de recursos para abrir a opção **Criar regras de controlo de aplicações**. Em **Selecionar VMs**, reveja a lista de VMs recomendadas e desmarque aquelas às quais não pretende aplicar o controlo de aplicações. Em **Selecionar processos para regras de inclusão em listas de permissões**, reveja a lista de aplicações recomendadas e desmarque aquelas às quais não pretende aplicar. A lista inclui:

   - **NOME**: O caminho completo do aplicativo
   - **PROCESSOS**: Quantos aplicativos residem em cada caminho
   - **COMUM**: "Sim" indica que esses processos foram executados na maioria das VMs neste grupo de recursos
   - **EXPLORÁVEL**: Um ícone de aviso indica se os aplicativos podem ser usados por um invasor para ignorar a lista de permissões do aplicativo. Recomenda-se que reveja estas aplicações antes da respetiva aprovação.

4. Depois de concluir as suas seleções, selecione **Criar**.

## <a name="clean-up-resources"></a>Limpar recursos
Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se pretender continuar a trabalhar com inícios rápidos e tutoriais posteriores, continue a executar o escalão Standard e mantenha o aprovisionamento automático ativado. Se não pretender continuar ou quiser voltar para o Escalão gratuito:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de Segurança**.
2. Selecione a subscrição ou a política para a qual pretende voltar como Gratuita. **Política de segurança** abre-se.
3. Em **COMPONENTES DA POLÍTICA**, selecione **Escalão de preço**.
4. Selecione **Gratuito** para alterar a subscrição, do Escalão standard para o Escalão gratuito.
5. Selecione **Guardar**.

Se pretender desativar aprovisionamento automático:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de segurança**.
2. Selecione a subscrição para a qual pretende desativar o aprovisionamento automático.
3. Em **Política de segurança – Recolha de Dados**, selecione **Desativar** em **Inclusão** para desativar o aprovisionamento automático.
4. Selecione **Guardar**.

>[!NOTE]
> Desativar o aprovisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure onde o agente tiver sido aprovisionado. Desativar o aprovisionamento automático limita a monitorização da segurança dos seus recursos.
>

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a limitar a exposição a ameaças ao:

> [!div class="checklist"]
> * Configurar uma política de acesso à VM just-in-time para fornecer acesso controlado e auditado a VMs apenas quando necessário
> * Configurar uma política de controlos de aplicações adaptáveis para controlar as aplicações que podem ser executadas nas suas VMs

Avance para o próximo tutorial para aprender a responder a incidentes de segurança.

> [!div class="nextstepaction"]
> [Tutorial: Responder a alertas de segurança](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
