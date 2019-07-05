---
title: 'Azure Active Directory Domain Services: Configuração do grupo de segurança de rede de resolução de problemas | Documentos da Microsoft'
description: Resolução de problemas de configuração de NSG para o Azure AD Domain Services
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
ms.openlocfilehash: 08875ec23740eab7787c4a919566df521deba9a5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473911"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Resolver problemas de configuração de rede inválida para o seu domínio gerido
Este artigo ajuda-o a resolver erros de configuração relacionadas com a rede que resultam na mensagem de alerta seguinte:

## <a name="alert-aadds104-network-error"></a>Alerta AADDS104: Erro de rede
**Mensagem de alerta:** *A Microsoft é não é possível alcançar os controladores de domínio para este domínio gerido. Isto pode acontecer se um grupo de segurança de rede (NSG) configurado na sua rede virtual bloqueia o acesso ao domínio gerido. Outra razão possível é se houver uma rota definida pelo utilizador que bloqueia o tráfego de entrada da internet.*

Configurações de NSG inválidas são a causa mais comum de erros de rede para serviços de domínio do Azure AD. O grupo de segurança de rede (NSG) configurado para a rede virtual tem de permitir o acesso ao [portas específicas](network-considerations.md#ports-required-for-azure-ad-domain-services). Se estas portas são bloqueadas, a Microsoft não é possível monitorizar ou atualizar o seu domínio gerido. Além disso, a sincronização entre o diretório do Azure AD e o seu domínio gerido é afetada. Ao criar o seu NSG, mantém estas portas abertas para evitar interrupções no serviço.

### <a name="checking-your-nsg-for-compliance"></a>A verificar o seu NSG para conformidade

1. Navegue para o [grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) página no portal do Azure
2. A partir da tabela, escolha o NSG associado à sub-rede na qual o seu domínio gerido está ativado.
3. Sob **configurações** no painel à esquerda, clique em **regras de segurança de entrada**
4. Reveja as regras no local e identificar quais regras estão a bloquear o acesso a [estas portas](network-considerations.md#ports-required-for-azure-ad-domain-services)
5. Edite o NSG para garantir a conformidade a eliminar a regra, adicionar uma regra, ou criando um novo NSG inteiramente. Os passos para [adicionar uma regra](#add-a-rule-to-a-network-security-group-using-the-azure-portal) ou crie um novo, em conformidade com NSG estão abaixo

## <a name="sample-nsg"></a>Exemplo de NSG
A tabela a seguir ilustra um NSG que seria manter seu domínio gerido segura, permitindo que a Microsoft para monitorizar, gerir e atualizar as informações de exemplo.

![sample NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> O Azure AD Domain Services requer acesso sem restrições de saída da rede virtual. Recomendamos que não para criar qualquer regra NSG adicional, que restringe o acesso de saída para a rede virtual.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Adicionar uma regra a um grupo de segurança de rede com o portal do Azure
Se não pretender utilizar o PowerShell, pode adicionar manualmente o únicas regras a NSGs com o portal do Azure. Para criar regras no seu grupo de segurança de rede, conclua os seguintes passos:

1. Navegue para o [grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) página no portal do Azure.
2. A partir da tabela, escolha o NSG associado à sub-rede na qual o seu domínio gerido está ativado.
3. Sob **configurações** no painel à esquerda, clique em **regras de segurança de entrada** ou **regras de segurança de saída**.
4. Criar a regra clicando **adicionar** e preenchendo as informações. Clique em **OK**.
5. Certifique-se de que a regra foi criada pela sua localização na tabela de regras.


## <a name="need-help"></a>Precisa de ajuda?
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para o suporte](contact-us.md).
