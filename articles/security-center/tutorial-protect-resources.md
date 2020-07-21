---
title: Acesso & controlos de aplicação tutorial - Azure Security Center
description: Este tutorial mostra-lhe como configurar uma política de acesso vm just-in-time e uma política de controlo de aplicações.
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
ms.openlocfilehash: 3e4404589e180be730579b8cbbfadd132502585a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529323"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Tutorial: proteger os seus recursos com o Centro de Segurança do Azure
O Centro de Segurança limita a exposição a ameaças ao utilizar controlos de acesso e aplicações para bloquear atividade maliciosa. O acesso à máquina virtual (VM) just-in-time (VM) reduz a sua exposição a ataques, permitindo-lhe negar o acesso persistente aos VM. Em alternativa, o utilizador fornece acesso controlado e auditado a VMs apenas quando necessário. Os controlos de aplicações adaptáveis ajudam a proteger as VMs contra software maligno ao controlar as aplicações que podem ser executadas nas suas VMs. O Centro de Segurança utiliza machine learning para analisar os processos que estão a ser executados na VM e ajuda-o a aplicar regras de inclusão nas listas de permissões com base nessas informações.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure uma política de acesso em VM just-in-time
> * Configurar uma política de controlo de aplicações

## <a name="prerequisites"></a>Pré-requisitos
Para analisar as funcionalidades abrangidas por este tutorial, você deve estar no nível de preços padrão do Security Center. Pode tentar o Security Center Standard sem custos. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Incluir a sua subscrição do Azure no Centro de Segurança Standard) explica-lhe como atualizar para Standard.

## <a name="manage-vm-access"></a>Gerir o acesso à VM
O acesso JIT VM pode ser utilizado para bloquear o tráfego de entrada nos seus VMs Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário.

As portas de gestão não precisam de estar abertas permanentemente. Apenas têm de estar abertas enquanto estiver ligado à VM, por exemplo, para realizar tarefas de gestão ou manutenção. Quando o tempo é permitido, o Security Center utiliza as regras do Network Security Group (NSG), que restringem o acesso às portas de gestão para que não possam ser alvo de agressores.

1. No menu principal do Centro de Segurança, selecione **acesso VM just-in-time** em **ADVANCED CLOUD DEFENSE**.

   ![Acesso just-in-time à VM][1]

   **O acesso em VM just-in-time** fornece informações sobre o estado dos seus VMs:

   - **Configurado** - VMs que foram configurados para suportar o acesso em VM just-in-time.
   - **Recomendado** - VMs que podem suportar o acesso em VM just-in-time, mas não foram configurados para.
   - **Nenhuma recomendação** - As razões que podem fazer com que uma VM não seja recomendada são:

     - NSG em falta - A solução just-in-time requer que um NSG esteja no lugar.
     - O acesso clássico do VM - Security Center just-in-time suporta apenas VMs implantados através do Azure Resource Manager.
     - Outro - Um VM está nesta categoria se a solução just-in-time for desligada na política de segurança da subscrição ou do grupo de recursos, ou se o VM estiver faltando um IP público e não tiver um NSG no lugar.

2. Selecione um VM recomendado e clique **em Enable JIT em 1 VM** para configurar uma política just-in-time para esse VM:

   Pode guardar as portas predefinidas que o Security Center recomenda ou pode adicionar e configurar uma nova porta na qual pretende ativar a solução just-in-time. Neste tutorial, vamos adicionar uma porta ao selecionar **Adicionar**.

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

   A secção **grupos de recursos** contém três separadores:

   - **Configurados**: lista de grupos de recursos que contêm as VMs que foram configuradas com o controlo de aplicações.
   - **Recomendados**: lista de grupos de recursos para os quais o controlo de aplicações é recomendado.
   - **Nenhuma recomendação**: lista de grupos de recursos que contêm as VMs para as quais não existem recomendações de controlo de aplicações. Por exemplo, VMs em que as aplicações estão sempre a ser alteradas e que não atingiram um estado estável.

2. Selecione o separador **Recomendados** para ver uma lista dos grupos de recursos com recomendações de controlo de aplicações.

   ![Recomendações de controlo de aplicações][4]

3. Selecione um grupo de recursos para abrir a opção **Criar regras de controlo de aplicações**. Em **Selecionar VMs**, reveja a lista de VMs recomendadas e desmarque aquelas às quais não pretende aplicar o controlo de aplicações. Em **Selecionar processos para regras de inclusão em listas de permissões**, reveja a lista de aplicações recomendadas e desmarque aquelas às quais não pretende aplicar. A lista inclui:

   - **NOME**: o caminho completo da aplicação
   - **PROCESSOS**: quantas aplicações residem em cada caminho
   - **COMUM**: "Sim" indica que estes processos foram executados na maioria dos VMs neste grupo de recursos
   - **EXPLOITABLE**: Um ícone de aviso indica se as aplicações podem ser usadas por um intruso para contornar a lista de identificação da aplicação. Recomenda-se que reveja estas aplicações antes da respetiva aprovação.

4. Depois de concluir as suas seleções, selecione **Criar**.

## <a name="clean-up-resources"></a>Limpar recursos
Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se pretender continuar a trabalhar com os rápidos e tutoriais subsequentes, continue a executar o nível padrão e mantenha o provisionamento automático ativado. Se não pretender continuar ou quiser voltar para o Escalão gratuito:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de Segurança**.
2. Selecione a subscrição ou a política para a qual pretende voltar como Gratuita. **Política de segurança** abre-se.
3. Em **COMPONENTES DA POLÍTICA**, selecione **Escalão de preço**.
4. Selecione **Livre** para alterar a subscrição do nível padrão para o nível Livre.
5. Selecione **Guardar**.

Se pretender desativar aprovisionamento automático:

1. Volte ao menu principal do Centro de Segurança e selecione **a política de segurança.**
2. Selecione a subscrição para a qual pretende desativar o aprovisionamento automático.
3. Em **Política de segurança – Recolha de Dados**, selecione **Desativar** em **Inclusão** para desativar o aprovisionamento automático.
4. Selecione **Guardar**.

>[!NOTE]
> A desativação do fornecimento automático não remove o agente Log Analytics dos VMs Azure onde o agente foi provisionado. Desativar o aprovisionamento automático limita a monitorização da segurança dos seus recursos.
>

## <a name="next-steps"></a>Próximos passos
Neste tutorial, aprendeu a limitar a exposição a ameaças ao:

> [!div class="checklist"]
> * Configurar uma política de acesso em VM just-in-time para fornecer acesso controlado e auditado aos VM apenas quando necessário
> * Configurar uma política de controlos de aplicações adaptáveis para controlar as aplicações que podem ser executadas nas suas VMs

Avance para o próximo tutorial para aprender a responder a incidentes de segurança.

> [!div class="nextstepaction"]
> [Tutorial: responder a incidentes de segurança](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
