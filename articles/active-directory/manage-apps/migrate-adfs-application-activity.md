---
title: Use o relatório de atividade para mover AD FS aplicativos para Azure Active Directory | Microsoft Docs '
description: O relatório de atividade do aplicativo Serviços de Federação do Active Directory (AD FS) (AD FS) permite que você migre rapidamente aplicativos do AD FS para o Azure Active Directory (Azure AD). Esta ferramenta de migração para AD FS identifica a compatibilidade com o Azure AD e fornece diretrizes de migração.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978023"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Usar o AD FS relatório de atividade do aplicativo (versão prévia) para migrar aplicativos para o Azure AD

Muitas organizações usam o Serviços de Federação do Active Directory (AD FS) (AD FS) para fornecer logon único para aplicativos em nuvem. Há benefícios significativos para mover seus AD FS aplicativos para o Azure AD para autenticação, especialmente em termos de gerenciamento de custos, gerenciamento de riscos, produtividade, conformidade e governança. Mas entender quais aplicativos são compatíveis com o Azure AD e identificar etapas de migração específicas pode ser demorado.

O AD FS relatório de atividade do aplicativo (versão prévia) no portal do Azure permite identificar rapidamente quais aplicativos podem ser migrados para o Azure AD. Ele avalia todos os aplicativos AD FS para compatibilidade com o Azure AD, verifica se há algum problema e fornece orientação sobre como preparar aplicativos individuais para migração. Com o AD FS relatório de atividade do aplicativo, você pode:

* **Descubra AD FS aplicativos e escopo sua migração.** O relatório de atividade do aplicativo AD FS lista todos os aplicativos AD FS em sua organização e indica sua preparação para a migração para o Azure AD.
* **Priorize os aplicativos para migração.** Obtenha o número de usuários exclusivos que entraram no aplicativo nos últimos 1, 7 ou 30 dias para ajudar a determinar a criticalidade ou o risco de migrar o aplicativo.
* **Executar testes de migração e corrigir problemas.** O serviço de relatório executa testes automaticamente para determinar se um aplicativo está pronto para migrar. Os resultados são exibidos no relatório de atividade do aplicativo AD FS como um status de migração. Se possíveis problemas de migração forem identificados, você obterá diretrizes específicas sobre como resolver os problemas.

Os dados de atividade do aplicativo AD FS estão disponíveis para os usuários que recebem qualquer uma dessas funções de administrador: administrador global, leitor de relatórios, leitor de segurança, administrador de aplicativos ou administrador de aplicativos de nuvem.

## <a name="prerequisites"></a>Pré-requisitos

* Sua organização deve estar atualmente usando AD FS para acessar aplicativos.
* Azure AD Connect Health deve ser habilitado em seu locatário do Azure AD.
   * [Saiba mais sobre o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Introdução à configuração de Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Descobrir AD FS aplicativos que podem ser migrados 

O relatório de atividade do aplicativo AD FS está disponível no portal do Azure em uso do Azure AD & relatórios de **informações** . O relatório de atividade do aplicativo AD FS analisa cada aplicativo AD FS para determinar se ele pode ser migrado no estado em que se encontra, ou se for necessária uma revisão adicional. 

1. Entre no [portal do Azure](https://portal.azure.com) com uma função de administrador que tenha acesso a AD FS dados de atividade do aplicativo (administrador global, leitor de relatórios, leitor de segurança, administrador de aplicativos ou administrador de aplicativos de nuvem).

2. Selecione **Azure Active Directory**e, em seguida, selecione **aplicativos empresariais**.

3. Em **atividade**, selecione **uso & insights (versão prévia)** e, em seguida, selecione **AD FS atividade do aplicativo** para abrir uma lista de todos os aplicativos AD FS em sua organização.

   ![AD FS atividade do aplicativo](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Para cada aplicativo na lista de atividades AD FS aplicativo, exiba o **status de migração**:

   * **Pronto para migrar** significa que a configuração do aplicativo AD FS tem suporte total no Azure AD e pode ser migrada no estado em que se encontra.

   * **Precisa de revisão** significa que algumas das configurações do aplicativo podem ser migradas para o Azure AD, mas você precisará examinar as configurações que não podem ser migradas no estado em que se encontram.

   * **Etapas adicionais necessárias** significa que o Azure ad não dá suporte a algumas das configurações do aplicativo, portanto, o aplicativo não pode ser migrado em seu estado atual.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Avaliar a prontidão de um aplicativo para migração 

1. Na lista AD FS atividade do aplicativo, clique no status na coluna **status da migração** para abrir os detalhes da migração. Você verá um resumo dos testes de configuração aprovados, juntamente com quaisquer possíveis problemas de migração.

   ![Detalhes de migração](media/migrate-adfs-application-activity/migration-details.png)

2. Clique em uma mensagem para abrir detalhes adicionais da regra de migração. Para obter uma lista completa das propriedades testadas, consulte a tabela [AD FS testes de configuração de aplicativo](#ad-fs-application-configuration-tests) , abaixo.

   ![Detalhes da regra de migração](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS testes de configuração de aplicativo

A tabela a seguir lista todos os testes de configuração que são executados em AD FS aplicativos.

|Resultado  |Aprovação/aviso/falha  |Descrição  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Pelo menos uma regra não migrada foi detectada para AdditionalAuthentication.       | Aprovação/aviso          | A terceira parte confiável tem regras para solicitar a autenticação multifator (MFA). Para mudar para o Azure AD, traduza essas regras em políticas de acesso condicional. Se você estiver usando uma MFA local, recomendamos que você mude para o Azure MFA. [Saiba mais sobre o acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> A terceira parte confiável tem AdditionalWSFedEndpoint definido como true.       | Aprovado/reprovado          | A terceira parte confiável no AD FS permite vários pontos de extremidade de asserção de WS-alimentados. Atualmente, o Azure AD dá suporte apenas a um. Se você tiver um cenário em que esse resultado está bloqueando a migração, [informe-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> A terceira parte confiável definiu AllowedAuthenticationClassReferences.       | Aprovado/reprovado          | Essa configuração no AD FS permite que você especifique se o aplicativo está configurado para permitir apenas determinados tipos de autenticação. É recomendável usar o acesso condicional para obter esse recurso.  Se você tiver um cenário em que esse resultado está bloqueando a migração, [informe-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Saiba mais sobre o acesso condicional](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Aprovado/reprovado          | Essa configuração no AD FS permite que você especifique se o aplicativo está configurado para ignorar cookies de SSO e **sempre solicitar autenticação**. No Azure AD, você pode gerenciar a sessão de autenticação usando políticas de acesso condicional para obter um comportamento semelhante. [Saiba mais sobre como configurar o gerenciamento de sessão de autenticação com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Test-ADFSRPAutoUpdateEnabled <br> A terceira parte confiável tem AutoUpdateEnabled definido como true       | Aprovação/aviso          | Essa configuração no AD FS permite especificar se AD FS está configurado para atualizar automaticamente o aplicativo com base nas alterações nos metadados da Federação. O Azure AD não dá suporte a isso hoje, mas não deve bloquear a migração do aplicativo para o Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> A terceira parte confiável tem vários ClaimsProviders habilitados       | Aprovado/reprovado          | Essa configuração em AD FS chama os provedores de identidade dos quais a terceira parte confiável está aceitando declarações. No Azure AD, você pode habilitar a colaboração externa usando o Azure AD B2B. [Saiba mais sobre o Azure ad B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Test-ADFSRPDelegationAuthorizationRules      | Aprovado/reprovado          | O aplicativo tem regras de autorização de delegação personalizadas definidas. Esse é um conceito de WS-Trust ao qual o Azure AD dá suporte usando protocolos de autenticação modernos, como OpenID Connect e OAuth 2,0. [Saiba mais sobre a plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Aprovação/aviso          | O aplicativo tem regras de autorização de representação personalizadas definidas. Esse é um conceito de WS-Trust ao qual o Azure AD dá suporte usando protocolos de autenticação modernos, como OpenID Connect e OAuth 2,0. [Saiba mais sobre a plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Pelo menos uma regra não migrada foi detectada para IssuanceAuthorization.       | Aprovação/aviso          | O aplicativo tem regras de autorização de emissão personalizadas definidas em AD FS. O Azure AD dá suporte a essa funcionalidade com acesso condicional do Azure AD. [Saiba mais sobre o acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> Você também pode restringir o acesso a um aplicativo por usuário ou grupos atribuídos ao aplicativo. [Saiba mais sobre como atribuir usuários e grupos para acessar aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|Test-ADFSRPIssuanceTransformRules <br> Pelo menos uma regra não migrada foi detectada para IssuanceTransform.       | Aprovação/aviso          | O aplicativo tem regras de transformação de emissão personalizadas definidas em AD FS. O Azure AD dá suporte à personalização das declarações emitidas no token. Para saber mais, confira [Personalizar declarações emitidas no token SAML para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).           |
|Test-ADFSRPMonitoringEnabled <br> A terceira parte confiável tem MonitoringEnabled definido como true.       | Aprovação/aviso          | Essa configuração no AD FS permite especificar se AD FS está configurado para atualizar automaticamente o aplicativo com base nas alterações nos metadados da Federação. O Azure AD não dá suporte a isso hoje, mas não deve bloquear a migração do aplicativo para o Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Aprovação/aviso          | AD FS permite uma distorção de tempo com base nas horas de não antes e NotOnOrAfter no token SAML. O Azure AD trata isso automaticamente por padrão.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> A terceira parte confiável tem RequestMFAFromClaimsProviders definido como true.       | Aprovação/aviso          | Essa configuração em AD FS determina o comportamento para MFA quando o usuário vem de um provedor de declarações diferente. No Azure AD, você pode habilitar a colaboração externa usando o Azure AD B2B. Em seguida, você pode aplicar políticas de acesso condicional para proteger o acesso de convidado. Saiba mais sobre o [Azure ad B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) e o [acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> A terceira parte confiável tem SignedSamlRequestsRequired definido como true       | Aprovado/reprovado          | O aplicativo é configurado no AD FS para verificar a assinatura na solicitação SAML. O Azure AD aceita uma solicitação SAML assinada; no entanto, ele não verificará a assinatura. O Azure AD tem métodos diferentes para proteger contra chamadas mal-intencionadas. Por exemplo, o AD do Azure usa as URLs de resposta configuradas no aplicativo para validar a solicitação SAML. O Azure AD enviará apenas um token para as URLs de resposta configuradas para o aplicativo. Se você tiver um cenário em que esse resultado está bloqueando a migração, [informe-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Aprovação/aviso         | O aplicativo está configurado para um tempo de vida de token personalizado. O padrão AD FS é de uma hora. O Azure AD dá suporte a essa funcionalidade usando o acesso condicional. Para saber mais, confira [Configurar o gerenciamento de sessão de autenticação com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|A terceira parte confiável está definida para criptografar declarações. Isso é suportado pelo Azure AD       | Passá          | Com o Azure AD, você pode criptografar o token enviado ao aplicativo. Para saber mais, confira [Configurar a criptografia de token SAML do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|EncryptedNameIdRequiredCheckResult      | Aprovado/reprovado          | O aplicativo está configurado para criptografar a declaração NameID no token SAML. Com o Azure AD, você pode criptografar todo o token enviado ao aplicativo. Ainda não há suporte para criptografia de declarações específicas. Para saber mais, confira [Configurar a criptografia de token SAML do Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Verificar os resultados dos testes de regra de declaração

Se você tiver configurado uma regra de declaração para o aplicativo no AD FS, a experiência fornecerá uma análise granular para todas as regras de declaração. Você verá quais regras de declaração podem ser movidas para o Azure AD e quais precisam de uma revisão adicional.

1. Na lista AD FS atividade do aplicativo, clique no status na coluna **status da migração** para abrir os detalhes da migração. Você verá um resumo dos testes de configuração aprovados, juntamente com quaisquer possíveis problemas de migração.

2. Na página **detalhes da regra de migração** , expanda os resultados para exibir detalhes sobre possíveis problemas de migração e para obter diretrizes adicionais. Para obter uma lista detalhada de todas as regras de declaração testadas, consulte a tabela [verificar os resultados de testes de regra de declaração](#check-the-results-of-claim-rule-tests) , abaixo.

   O exemplo a seguir mostra os detalhes da regra de migração para a regra IssuanceTransform. Ele lista as partes específicas da declaração que precisam ser examinadas e abordadas antes que você possa migrar o aplicativo para o Azure AD.

   ![Diretrizes adicionais de detalhes da regra de migração](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Testes de regra de declaração

A tabela a seguir lista todos os testes de regra de declaração que são executados em AD FS aplicativos.

|Propriedade  |Descrição  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | A instrução Condition usa expressões regulares para avaliar se a declaração corresponde a um determinado padrão.  Para obter uma funcionalidade semelhante no Azure AD, você pode usar a transformação predefinida, como IfEmpty (), StartWith (), Contains (), entre outras. Para obter mais informações, consulte [Personalizar declarações emitidas no token SAML para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | A instrução Condition tem várias condições que precisam ser avaliadas antes da execução da instrução de emissão. O Azure AD pode dar suporte a essa funcionalidade com as funções de transformação da declaração, onde você pode avaliar vários valores de declaração.  Para obter mais informações, consulte [Personalizar declarações emitidas no token SAML para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | Não foi possível reconhecer a regra de declaração. Para obter mais informações sobre como configurar declarações no Azure AD, consulte [Personalizar declarações emitidas no token SAML para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | A instrução Condition usa um emissor que não tem suporte no Azure AD. Atualmente, o Azure AD não faz declarações de fontes de armazenamentos diferentes que Active Directory ou o Azure AD. Se isso estiver impedindo a migração de aplicativos para o Azure AD, [informe-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | A instrução Condition usa uma função de agregação para emitir ou adicionar uma única declaração, independentemente do número de correspondências.  No Azure AD, você pode avaliar o atributo de um usuário para decidir qual valor usar para a declaração com funções como IfEmpty (), StartWith (), Contains (), entre outras. Para obter mais informações, consulte [Personalizar declarações emitidas no token SAML para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | A instrução Condition usa uma declaração que é restrita no Azure AD. Você pode emitir uma declaração restrita, mas não pode modificar sua origem ou aplicar qualquer transformação. Para obter mais informações, consulte [Personalizar declarações emitidas em tokens para um aplicativo específico no Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | A instrução de emissão usa um repositório de atributos diferente daquele Active Directory. Atualmente, o Azure AD não faz declarações de fontes de armazenamentos diferentes que Active Directory ou o Azure AD. Se esse resultado estiver impedindo a migração de aplicativos para o Azure AD, [informe-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | A instrução de emissão usa adicionar para adicionar declarações ao conjunto de declarações de entrada. No Azure AD, isso pode ser configurado como várias transformações de declaração.  Para obter mais informações, consulte [Personalizar declarações emitidas no token SAML para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | A instrução de emissão usa expressões regulares para transformar o valor da declaração a ser emitida. Para obter uma funcionalidade semelhante no Azure AD, você pode usar a transformação predefinida, como Extract (), Trim (), ToLower, entre outras. Para obter mais informações, consulte [Personalizar declarações emitidas no token SAML para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Passos seguintes

- [Vídeo: como usar o relatório de AD FS atividade para migrar um aplicativo](https://www.youtube.com/watch?v=OThlTA239lU)
- [Managing applications with Azure Active Directory](what-is-application-management.md) (Gerir aplicações com o Azure Active Directory)
- [Gerir o acesso a aplicações](what-is-access-management.md)
- [Azure AD Connect Federation](../hybrid/how-to-connect-fed-whatis.md) (Federação do Azure AD Connect)
