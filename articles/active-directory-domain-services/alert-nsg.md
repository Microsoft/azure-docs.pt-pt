---
title: 'Azure Active Directory Domain Services: Solucionar problemas de grupos de segurança de rede | Microsoft Docs'
description: Solucionando problemas de configuração do grupo de segurança de rede para Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 450ee5635b378ed7c4d4e4bedc1c4245f6b52d70
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "70743439"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Solucionar problemas de configuração de rede inválida para seu domínio gerenciado
Este artigo ajuda você a solucionar problemas e resolver erros de configuração relacionados à rede que resultam na seguinte mensagem de alerta:

## <a name="alert-aadds104-network-error"></a>AADDS104 de alerta: Erro de rede
**Mensagem de alerta:** *A Microsoft não consegue acessar os controladores de domínio para este domínio gerenciado. Isso pode acontecer se um NSG (grupo de segurança de rede) configurado em sua rede virtual bloquear o acesso ao domínio gerenciado. Outro motivo possível é se há uma rota definida pelo usuário que bloqueia o tráfego de entrada da Internet.*

Configurações de NSG inválidas são a causa mais comum de erros de rede para Azure AD Domain Services. O NSG (grupo de segurança de rede) configurado para sua rede virtual deve permitir o acesso a [portas específicas](network-considerations.md#network-security-groups-and-required-ports). Se essas portas estiverem bloqueadas, a Microsoft não poderá monitorar ou atualizar seu domínio gerenciado. Além disso, a sincronização entre o diretório do Azure AD e o domínio gerenciado é afetada. Ao criar seu NSG, mantenha essas portas abertas para evitar a interrupção no serviço.

### <a name="checking-your-nsg-for-compliance"></a>Verificando a conformidade do NSG

1. Navegue até a página [grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) no portal do Azure
2. Na tabela, escolha o NSG associado à sub-rede na qual seu domínio gerenciado está habilitado.
3. Em **configurações** no painel esquerdo, clique em regras de **segurança de entrada**
4. Examine as regras em vigor e identifique quais regras estão bloqueando o acesso a [essas portas](network-considerations.md#network-security-groups-and-required-ports)
5. Edite o NSG para garantir a conformidade excluindo a regra, adicionando uma regra ou criando totalmente uma nova NSG. Etapas para [Adicionar uma regra](#add-a-rule-to-a-network-security-group-using-the-azure-portal) ou criar um novo NSG em conformidade estão abaixo

## <a name="sample-nsg"></a>NSG de exemplo
A tabela a seguir descreve um exemplo de NSG que manteria seu domínio gerenciado seguro, permitindo que a Microsoft monitore, gerencie e atualize informações.

![NSG de exemplo](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services requer acesso irrestrito de saída da rede virtual. Recomendamos não criar nenhuma regra NSG adicional que restringe o acesso de saída para a rede virtual.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Adicionar uma regra a um grupo de segurança de rede usando o portal do Azure
Se você não quiser usar o PowerShell, poderá adicionar manualmente as regras únicas para NSGs usando o portal do Azure. Para criar regras em seu grupo de segurança de rede, conclua as seguintes etapas:

1. Navegue até a página [grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) no portal do Azure.
2. Na tabela, escolha o NSG associado à sub-rede na qual seu domínio gerenciado está habilitado.
3. Em **configurações** no painel esquerdo, clique em **regras de segurança de entrada** ou em regras de segurança de **saída**.
4. Crie a regra clicando em **Adicionar** e preenchendo as informações. Clique em **OK**.
5. Verifique se sua regra foi criada localizando-a na tabela de regras.


## <a name="need-help"></a>Precisa de ajuda?
Entre em contato com a equipe de produto Azure Active Directory Domain Services para [compartilhar comentários ou para obter suporte](contact-us.md).
