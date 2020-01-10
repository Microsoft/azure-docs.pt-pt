---
title: Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy
description: Um artigo que descreve como usar Azure Policy para habilitar o backup automaticamente para todas as VMs criadas em um determinado escopo
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7a3b526d654936d4e7ec89127a9074146c1b0179
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450125"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Ativar automaticamente a cópia de segurança no momento da criação da VM com o Azure Policy

Uma das principais responsabilidades de um administrador de backup ou conformidade em uma organização é garantir que todos os computadores críticos para os negócios sejam submetidos a backup com a retenção apropriada.

Hoje, o backup do Azure fornece uma política interna (usando Azure Policy) que pode ser atribuída a **todas as VMs do Azure em um local especificado dentro de uma assinatura ou grupo de recursos**. Quando essa política é atribuída a um determinado escopo, todas as novas VMs criadas nesse escopo são automaticamente configuradas para backup em um **cofre existente no mesmo local e assinatura**. O usuário pode especificar o cofre e a política de retenção à qual as VMs de backup devem ser associadas.

## <a name="supported-scenarios"></a>Cenários Suportados 

* Atualmente, a política interna tem suporte apenas para VMs do Azure. Os usuários devem tomar cuidado para garantir que a política de retenção especificada durante a atribuição seja uma política de retenção de VM. Consulte [este](https://aka.ms/PolicySupportedSKUs) documento para ver todos os SKUs de VM com suporte nesta política.

* A política pode ser atribuída a um único local e assinatura de cada vez. Para habilitar o backup para VMs em locais e assinaturas, várias instâncias da atribuição de política precisam ser criadas, uma para cada combinação de localização e assinatura.

* O cofre especificado e as VMs configuradas para backup podem estar em grupos de recursos diferentes.

* O escopo do grupo de gerenciamento não tem suporte no momento.

* Atualmente, a política interna não está disponível em nuvens nacionais.

## <a name="using-the-built-in-policy"></a>Usando a política interna

Para atribuir a política ao escopo necessário, siga as etapas abaixo:

1. Entre no portal do Azure e navegue até o painel de **política** .
2. Selecione **definições** no menu à esquerda para obter uma lista de todas as políticas internas nos recursos do Azure.
3. Filtre a lista para **Category = backup**. Você verá a lista filtrada para uma única política denominada ' Configurar backup em VMs de um local para um cofre central existente no mesmo local '.
Painel de políticas de ![](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Clique no nome da política. Você será redirecionado para a definição detalhada dessa política.
Folha de definição de política de ![](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Clique no botão **atribuir** na parte superior da folha. Isso redireciona você para a folha **atribuir política** .
6. Em **noções básicas**, clique nos três pontos ao lado do campo **escopo** . Isso abre uma folha de contexto à direita, na qual você pode selecionar a assinatura na qual a política será aplicada. Você também pode selecionar um grupo de recursos, para que a política seja aplicada somente a VMs em um determinado grupo de recursos.
Noções básicas de atribuição de política de ![](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Na guia **parâmetros** , escolha um local na lista suspensa e selecione o cofre e a política de backup aos quais as VMs no escopo devem ser associadas.
![parâmetros de atribuição de política](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Verifique se o **efeito** está definido como deployIfNotExists.
9. Navegue até **revisar + criar** e clique em **criar**.

> [!NOTE]
>
> Azure Policy também pode ser usado em VMs existentes, usando a [correção](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> É recomendável que essa política não esteja atribuída a mais de 200 VMs por vez. Se a política for atribuída a mais de 200 VMs, isso poderá fazer com que o backup seja disparado algumas horas depois daquela especificada pela agenda.

## <a name="next-steps"></a>Próximos Passos

[Saiba mais sobre o Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)