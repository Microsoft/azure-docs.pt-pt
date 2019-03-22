---
title: Resolução de problemas híbrida do Azure Active Directory associados a dispositivos Windows 10 e Windows Server 2016 | Documentos da Microsoft
description: Resolução de problemas híbrida do Azure Active Directory associados a dispositivos Windows 10 e Windows Server 2016.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae53d8b0de6c9d73eb43524e31a2f55690390c3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004955"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Resolução de problemas híbrida do Azure Active Directory associados a dispositivos Windows 10 e Windows Server 2016 

Este artigo é aplicável aos seguintes clientes:

-   Windows 10
-   Windows Server 2016

Para outros clientes Windows, consulte [híbrido de resolução de problemas do Azure Active Directory associados a um nível inferior dispositivos](troubleshoot-hybrid-join-windows-legacy.md).

Este artigo pressupõe que tenha [dispositivos associados ao configurado híbrida do Azure Active Directory](hybrid-azuread-join-plan.md) para suportar os cenários seguintes:

- Acesso condicional baseado no dispositivo

- [Definições de roaming empresarial](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello para empresas](../active-directory-azureadjoin-passport-deployment.md)


Este documento fornece orientações de resolução de problemas sobre como resolver problemas potenciais. 


Para Windows 10 e Windows Server 2016, híbrido associação do Azure Active Directory suporta o Windows 10 de Novembro de 2015, atualizar e acima. Recomendamos que utilize a atualização de aniversário.

## <a name="step-1-retrieve-the-join-status"></a>Passo 1: Obter o estado de associação 

**Para obter o estado de associação:**

1. Abra a linha de comandos como administrador

2. Tipo de **dsregcmd /status**



```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```



## <a name="step-2-evaluate-the-join-status"></a>Passo 2: Avaliar o estado de associação 

Reveja os seguintes campos e certifique-se de que têm os valores esperados:

### <a name="azureadjoined--yes"></a>AzureAdJoined : SIM  

Este campo indica se o dispositivo está associado com o Azure AD. Se o valor for **não**, a associação ao Azure AD ainda não foi concluída. 

**Causas possíveis:**

- Falha na autenticação do computador para uma associação.

- Existe um proxy de HTTP na organização que não puderem ser detetada pelo computador

- O computador não é possível contactar o Azure AD para autenticar ou DRS do Azure para o registo

- O computador não pode estar na rede interna da organização ou VPN com a linha de visão direta com um local do controlador de domínio do AD.

- Se o computador tiver um TPM, pode estar num Estado incorreto.

- Pode haver uma configuração incorreta nos serviços observamos no documento que terá de verificar novamente. Alguns exemplos comuns são:

    - Servidor de Federação não tem pontos de extremidade do WS-Trust ativados

    - Servidor de Federação não permite a autenticação de entrada de computadores na sua rede através da autenticação integrada do Windows.

    - Não existe nenhum objeto de ponto de ligação de serviço que aponta para o seu nome de domínio verificado no Azure AD na floresta do AD em que o computador pertence a

---

### <a name="domainjoined--yes"></a>DomainJoined : SIM  

Este campo indica se o dispositivo estiver associado a um Active Directory no local ou não. Se o valor for **não**, o dispositivo não é possível efetuar uma associação do Azure AD híbrido.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: NO  

Este campo indica se o dispositivo está registado no Azure AD como um dispositivo pessoal (marcados como *à área de trabalho*). Este valor deve ser **não** para um computador associado a um domínio que também é associado ao Azure AD híbrido. Se o valor for **Sim**, uma conta escolar ou profissional foi adicionada antes da conclusão da associação do Azure AD híbrido. Neste caso, a conta é ignorada quando utilizar a versão de atualização de aniversário do Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : Sim e AzureADPrt: SIM
  
Estes campos indicam se o utilizador foi autenticado com êxito para o Azure AD ao iniciar sessão no dispositivo. Se os valores forem **não**, é possível devido:

- Chave de armazenamento ruim (STK) no TPM associado ao dispositivo no registo (Verifique a KeySignTest durante a execução privilegiada).

- ID de início de sessão alternativo

- Não foi encontrado o Proxy de HTTP

## <a name="next-steps"></a>Passos Seguintes

Para perguntas, consulte o [perguntas frequentes de gestão de dispositivos](faq.md) 
