---
title: 'Azure Active Directory Domain Services: Resolução de problemas de configuração de Secure LDAP | Documentos da Microsoft'
description: Resolução de problemas de Secure LDAP para serviços de domínio do Azure AD
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: dde4a02e5be32d5549ba499742d1ab650fa146c0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246588"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Serviços de domínio do Azure AD - configuração de resolução de problemas do LDAP seguro

Este artigo fornece resoluções para comuns com problemas ao [configurar o secure LDAP](configure-ldaps.md) para serviços de domínio do Azure AD.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Configuração do grupo de segurança de rede LDAP seguro

**Mensagem de alerta:**

*Secure LDAP via internet está ativada para o domínio gerido. No entanto, o acesso à porta 636 não está bloqueado através de um grupo de segurança de rede. Isso pode expor as contas de utilizador no domínio gerido a ataques de força bruta de palavra-passe.*

### <a name="secure-ldap-port"></a>Porta LDAP segura

Quando o LDAP seguro está ativado, recomendamos que crie regras adicionais para permitir o acesso de entrada de LDAPS apenas a partir de certos endereços IP. Estas regras protejam contra ataques de força bruta que poderiam representar uma ameaça de segurança para o seu domínio. Porta 636 permite o acesso ao seu domínio gerido. Eis como atualizar o seu NSG para permitir o acesso de Secure LDAP:

1. Navegue para o [guia de grupos de segurança de rede](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) no portal do Azure
2. Escolha o NSG associado com o domínio da tabela.
3. Clique em **regras de segurança de entrada**
4. Criar a regra de porta 636
   1. Clique em **adicionar** na barra de navegação superior.
   2. Escolher **endereços IP** para a origem.
   3. Especifique os intervalos de porta de origem para esta regra.
   4. Entrada "636" para os intervalos de portas de destino.
   5. Protocolo é **TCP**.
   6. Dê a regra de um nome apropriado, a descrição e a prioridade. Prioridade desta regra deve ser superior a prioridade de sua regra de "Negar todos os", se tiver uma.
   7. Clique em **OK**.
5. Certifique-se de que a regra foi criada.
6. Verifique o estado de funcionamento do seu domínio nas duas horas para se certificar de que concluiu os passos corretamente.

> [!TIP]
> Porta 636 não é a única regra necessária para os serviços de domínio do Azure AD sem problemas. Para saber mais, visite o [orientações de redes](network-considerations.md) ou [configuração de NSG de resolução de problemas](alert-nsg.md) artigos.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Proteger a expiração do certificado LDAP

**Mensagem de alerta:**

*O certificado de secure LDAP para o domínio gerido irá expirar em [date]].*

**Resolução:**

Criar um novo certificado de secure LDAP ao seguir os passos descritos no [configurar o secure LDAP](configure-ldaps.md) artigo.

## <a name="contact-us"></a>Contacte-nos
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para o suporte](contact-us.md).
