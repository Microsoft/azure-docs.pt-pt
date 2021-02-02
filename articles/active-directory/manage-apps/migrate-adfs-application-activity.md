---
title: Utilize o relatório de atividades para mover as aplicações AD FS para o Azure Ative Directory | Microsoft Docs'
description: O relatório de atividade da Federação de Diretório Ativo (AD FS) permite migrar rapidamente as aplicações da AD FS para o Azure Ative Directory (Azure AD). Esta ferramenta de migração para AD FS identifica a compatibilidade com a Azure AD e dá orientação de migração.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3665c5b82095004ddf7dc1f503b54f5164d49c7f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260067"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Utilize o relatório de atividade da aplicação AD FS (pré-visualização) para migrar aplicações para Azure AD

Muitas organizações usam serviços da Federação de Diretórios Ativos (AD FS) para fornecer um único sinal de inscrição para aplicações na nuvem. Existem benefícios significativos na deslocação das suas aplicações AD FS para Azure AD para autenticação, especialmente em termos de gestão de custos, gestão de risco, produtividade, conformidade e governação. Mas compreender quais aplicações são compatíveis com a Azure AD e identificar passos de migração específicos pode ser demorado.

O relatório de atividade da aplicação AD FS (pré-visualização) no portal Azure permite identificar rapidamente quais das suas aplicações são capazes de ser migradas para Azure AD. Avalia todos os pedidos de compatibilidade da AD FS com a Azure AD, verifica quaisquer problemas e orienta-se sobre a preparação de pedidos individuais de migração. Com o relatório de atividades da aplicação da AD FS, pode:

* **Descubra aplicações AD FS e abranga a sua migração.** O relatório de atividade da aplicação AD FS lista todas as aplicações AD FS na sua organização que tiveram um login de utilizador ativo nos últimos 30 dias. O relatório indica uma prontidão de aplicações para a migração para Azure AD. O relatório não mostra as partes relacionadas com a Microsoft em FS AD, como o Office 365. Por exemplo, confiar em festas com o nome 'urn:federation:MicrosoftOnline'.

* **Priorizar candidaturas para migração.** Obtenha o número de utilizadores únicos que se inscreveram na aplicação nos últimos 1, 7 ou 30 dias para ajudar a determinar a criticidade ou o risco de migrar a aplicação.
* **Fazer testes de migração e corrigir problemas.** O serviço de reporte executa automaticamente testes para determinar se uma aplicação está pronta para migrar. Os resultados são apresentados no relatório de atividade da aplicação da AD FS como um estado de migração. Se a configuração AD FS não for compatível com uma configuração AD Azure, obtém orientações específicas sobre como abordar a configuração em Azure AD.

Os dados da atividade da aplicação AD FS estão disponíveis para os utilizadores que recebem qualquer uma destas funções de administração: administrador global, leitor de relatórios, leitor de segurança, administrador de aplicação ou administrador de aplicações na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

* A sua organização deve estar atualmente a usar AD FS para aceder a aplicações.
* A Azure AD Connect Health deve ser ativado no seu inquilino AZure AD.
* O Azure AD Connect Health para o agente AD FS deve ser instalado.
   * [Saiba mais sobre a Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Começar com a configuração do Azure AD Connect Health e instalar o agente AD FS](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Existem algumas razões para não ver todas as aplicações que espera depois de ter instalado o Azure AD Connect Health. O relatório de atividade da aplicação AD FS apenas mostra as partes que contam com logins de utilizadores nos últimos 30 dias. Além disso, o relatório não mostrará partes relacionadas com a Microsoft, como o Office 365.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Descubra aplicações AD FS que podem ser migradas 

O relatório de atividade da aplicação AD FS está disponível no portal Azure ao abrigo do uso de Azure AD **& relatórios de insights.** O relatório de atividade da aplicação AD FS analisa cada aplicação AD FS para determinar se pode ser migrado como está, ou se é necessário um reexame adicional. 

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma função de administrador que tenha acesso aos dados de atividade da aplicação AD FS (administrador global, leitor de relatórios, leitor de segurança, administrador de aplicações ou administrador de aplicações na nuvem).

2. Selecione **Azure Ative Directory** e, em seguida, selecione **aplicações Enterprise**.

3. Em **Atividade**, selecione **Usage & Insights (Preview)** e, em seguida, selecione a atividade de **aplicação AD FS** para abrir uma lista de todas as aplicações AD FS na sua organização.

   ![Atividade de aplicação AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Para cada aplicação na lista de atividades de aplicação da AD FS, consulte o **estado da migração:**

   * **Pronto para migrar** significa que a configuração da aplicação AD FS é totalmente suportada em Azure AD e pode ser migrada como está.

   * **As necessidades** de revisão significam que algumas das definições da aplicação podem ser migradas para Azure AD, mas terá de rever as definições que não podem ser migradas como está.

   * **As medidas adicionais necessárias** significam que o Azure AD não suporta algumas das configurações da aplicação, pelo que a aplicação não pode ser migrada no seu estado atual.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Avaliar a prontidão de um pedido de migração 

1. Na lista de atividades de aplicação da AD FS, clique no estado na coluna **do estado da migração** para abrir detalhes da migração. Você verá um resumo dos testes de configuração que passaram, juntamente com quaisquer potenciais problemas de migração.

   ![Detalhes de migração](media/migrate-adfs-application-activity/migration-details.png)

2. Clique numa mensagem para abrir detalhes adicionais da regra de migração. Para obter uma lista completa das propriedades testadas, consulte a tabela de testes de configuração da [aplicação AD FS,](#ad-fs-application-configuration-tests) abaixo.

   ![Detalhes da regra da migração](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Testes de configuração de aplicação AD FS

A tabela que se segue lista todos os testes de configuração que são realizados em aplicações AD FS.

|Resultado  |Passe/Aviso/Falha  |Descrição  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Pelo menos uma regra não migradora foi detetada para a Autorização Adicional.       | Passe/Aviso          | A parte dependente tem regras para solicitar a autenticação de vários fatores (MFA). Para passar para Azure AD, traduza essas regras em políticas de Acesso Condicional. Se estiver a utilizar um MFA no local, recomendamos que se mude para o Azure AD MFA. [Saiba mais sobre o Acesso Condicional.](../authentication/concept-mfa-howitworks.md)        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> A parte de contemitimento tem AdicionalWSFedEndpoint definido para verdadeiro.       | Aprovado/Reprovado          | A parte de confiança em AD FS permite múltiplos pontos finais de afirmação WS-Fed.Atualmente, a AZure AD só suporta uma.Se tiver um cenário em que este resultado esteja a bloquear a migração, [avise-nos.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> O Partido De Confiar definiu AsReferências De Classe Permitidas.       | Aprovado/Reprovado          | Esta definição em AD FS permite especificar se a aplicação está configurada apenas para permitir certos tipos de autenticação. Recomendamos a utilização de Acesso Condicional para alcançar esta capacidade. Se tiver um cenário em que este resultado esteja a bloquear a migração, [avise-nos.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)  [Saiba mais sobre o Acesso Condicional.](../authentication/concept-mfa-howitworks.md)          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Aprovado/Reprovado          | Esta definição em AD FS permite especificar se a aplicação está configurada para ignorar cookies SSO e **Sempre Pronta para Autenticação.** No Azure AD, pode gerir a sessão de autenticação utilizando políticas de Acesso Condicional para obter comportamentos semelhantes. [Saiba mais sobre configurar a gestão da sessão de autenticação com Acesso Condicional.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|Test-ADFSRPAutoUpdateEnabled <br> Partido De Relying tem AutoUpdateEnabled definido para verdadeiro       | Passe/Aviso          | Esta definição em AD FS permite especificar se o AD FS está configurado para atualizar automaticamente a aplicação com base em alterações dentro dos metadados da federação. A Azure AD não apoia isso hoje, mas não deve bloquear a migração da aplicação para Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> A Parte De Confiança tem vários Pedidos de Crédito habilitados       | Aprovado/Reprovado          | Esta definição em AD FS chama os fornecedores de identidade a partir dos quais a parte que conta está aceitando reclamações. Em Azure AD, pode ativar a colaboração externa utilizando o Azure AD B2B. [Saiba mais sobre o Azure AD B2B](../external-identities/what-is-b2b.md).          |
|Test-ADFSRPDelegationAuthorizationRules      | Aprovado/Reprovado          | O pedido tem regras de autorização de delegação personalizadas definidas. Este é um conceito WS-Trust que a Azure AD suporta utilizando protocolos de autenticação modernos, como o OpenID Connect e o OAuth 2.0. [Saiba mais sobre a Plataforma de Identidade da Microsoft.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPImpersonationAuthorizationRules       | Passe/Aviso          | A aplicação tem regras de autorização de imitação personalizadas definidas.Este é um conceito WS-Trust que a Azure AD suporta utilizando protocolos de autenticação modernos, como o OpenID Connect e o OAuth 2.0. [Saiba mais sobre a Plataforma de Identidade da Microsoft.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Pelo menos uma regra não migradora foi detetada para a emissão de anestesia.       | Passe/Aviso          | A aplicação tem regras de autorização de emissão personalizadas definidas em FS AD.A Azure AD suporta esta funcionalidade com acesso condicional Azure AD. [Saiba mais sobre o Acesso Condicional.](../conditional-access/overview.md) <br> Também pode restringir o acesso a uma aplicação por utilizador ou grupos atribuídos à aplicação. [Saiba mais sobre a atribuição de utilizadores e grupos para aceder a aplicações.](./assign-user-or-group-access-portal.md)            |
|Test-ADFSRPIssuanceTransformRules <br> Pelo menos uma regra não migradora foi detetada para o IssuanceTransform.       | Passe/Aviso          | A aplicação tem regras de transformação de emissão personalizada definidas em FS AD. A Azure AD suporta a personalização das reclamações emitidas no token. Para saber mais, consulte [Personalizar reclamações emitidas no token SAML para aplicações empresariais.](../develop/active-directory-saml-claims-customization.md)           |
|Test-ADFSRPMonitoringEnabled <br> O Partido De singing tem monitoringEnabled definido para verdade.       | Passe/Aviso          | Esta definição em AD FS permite especificar se o AD FS está configurado para atualizar automaticamente a aplicação com base em alterações dentro dos metadados da federação. A Azure AD não apoia isso hoje, mas não deve bloquear a migração da aplicação para Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotForeSkewCheckResult      | Passe/Aviso          | O AD FS permite um desvio de tempo baseado nos tempos notBefore e NotOnOrAfter no token SAML. O Azure AD trata-o automaticamente por defeito.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> A Parte De Relying tem RequestMFAFromClaimsProviders definido para verdadeiro.       | Passe/Aviso          | Esta definição em AD FS determina o comportamento do MFA quando o utilizador provém de um fornecedor de reclamações diferente. Em Azure AD, pode ativar a colaboração externa utilizando o Azure AD B2B. Em seguida, pode aplicar políticas de Acesso Condicional para proteger o acesso dos hóspedes. Saiba mais sobre [o Azure AD B2B](../external-identities/what-is-b2b.md) e [o Acesso Condicional.](../conditional-access/overview.md)          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Partido De Relying assinou SamlRequestsRequired definido para verdadeiro       | Aprovado/Reprovado          | O pedido está configurado em AD FS para verificar a assinatura no pedido SAML. A Azure AD aceita um pedido de SAML assinado; no entanto, não verificará a assinatura. A Azure AD tem diferentes métodos para proteger contra chamadas maliciosas. Por exemplo, a Azure AD utiliza os URLs de resposta configurados no pedido para validar o pedido DEL. A Azure AD só enviará um símbolo para responder aos URLs configurados para o pedido. Se tiver um cenário em que este resultado esteja a bloquear a migração, [avise-nos.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Passe/Aviso         | A aplicação está configurada para uma vida útil personalizada. O padrão da AD FS é de uma hora.A Azure AD suporta esta funcionalidade usando Acesso Condicional. Para saber mais, consulte a gestão da [sessão de autenticação configurada com Acesso Condicional.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|O Partido De Gestão está preparado para encriptar as alegações. Isto é apoiado pela Azure AD       | Aprovação          | Com a Azure AD, pode encriptar o token enviado para a aplicação. Para saber mais, consulte a [encriptação do token Configure AD SAML](./howto-saml-token-encryption.md).          |
|EncriptadoNameIdRequiredCheckResult      | Aprovado/Reprovado          | A aplicação está configurada para encriptar a reclamação nameID no token SAML.Com a Azure AD, pode encriptar todo o token enviado para a aplicação.A encriptação de alegações específicas ainda não está suportada. Para saber mais, consulte a [encriptação do token Configure AD SAML](./howto-saml-token-encryption.md).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Verifique os resultados dos testes de regras de reclamação

Se tiver configurado uma regra de reclamação para a aplicação em AD FS, a experiência fornecerá uma análise granular para todas as regras de reclamação. Verá quais as regras de reclamação que podem ser transferidas para a Azure AD e quais precisam de ser revistas.

1. Na lista de atividades de aplicação da AD FS, clique no estado na coluna **do estado da migração** para abrir detalhes da migração. Você verá um resumo dos testes de configuração que passaram, juntamente com quaisquer potenciais problemas de migração.

2. Na página de detalhes da **regra da migração,** expanda os resultados para mostrar detalhes sobre potenciais problemas de migração e para obter orientações adicionais. Para obter uma lista detalhada de todas as regras de reclamação testadas, consulte o [quadro de resultados dos testes de regras de reclamação,](#check-the-results-of-claim-rule-tests) abaixo.

   O exemplo abaixo mostra detalhes da regra de migração para a regra IssuanceTransform. Ele lista as partes específicas da alegação que precisam de ser revistas e endereçadas antes de poder migrar a aplicação para Azure AD.

   ![Regra de migração detalha orientação adicional](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Testes de regra de reivindicação

A tabela que se segue lista todos os testes de regras de reclamação que são realizados em aplicações AD FS.

|Propriedade  |Descrição  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | A declaração da condição utiliza Expressões Regulares para avaliar se a reclamação corresponde a um determinado padrão.Para obter uma funcionalidade semelhante em Azure AD, pode utilizar uma transformação pré-definida como IfEmpty(), StartWith(), Contains(), entre outros. Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicações empresariais.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_CONDITION_CLASS      | A declaração da condição tem múltiplas condições que precisam de ser avaliadas antes de executar a declaração de emissão.O Azure AD pode suportar esta funcionalidade com as funções de transformação da reclamação, onde pode avaliar vários valores de reclamação.Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicações empresariais.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_RULE_TYPE      | A regra da reivindicação não podia ser reconhecida. Para obter mais informações sobre como configurar reclamações em Azure AD, consulte [Personalizar reclamações emitidas no token SAML para aplicações empresariais](../develop/active-directory-saml-claims-customization.md).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | A declaração da condição usa um Emitente que não é suportado no Azure AD.Atualmente, a Azure AD não fornece alegações de lojas diferentes que Ative Directory ou Azure AD. Se isto está a impedi-lo de migrar aplicações para a Azure AD, [avise-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | A declaração da condição usa uma função agregada para emitir ou adicionar uma única reclamação, independentemente do número de partidas.Em Azure AD, pode avaliar o atributo de um utilizador para decidir qual o valor a utilizar para a reclamação com funções como IfEmpty(), StartWith(), Contém(), entre outros.Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicações empresariais.](../develop/active-directory-saml-claims-customization.md)          |
|RESTRICTED_CLAIM_ISSUED      | A declaração da condição usa uma alegação restrita em Azure AD. Pode ser capaz de emitir uma reclamação restrita, mas não pode modificar a sua fonte ou aplicar qualquer transformação. Para obter mais informações, consulte [Personalizar reclamações emitidas em fichas para uma aplicação específica em Azure AD](../develop/active-directory-claims-mapping.md).          |
|EXTERNAL_ATTRIBUTE_STORE      | A declaração de emissão utiliza uma loja de atributos diferente do Ative Directory. Atualmente, a Azure AD não fornece alegações de lojas diferentes que Ative Directory ou Azure AD. Se este resultado estiver a impedi-lo de migrar aplicações para a Azure AD, [avise-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | A declaração de emissão utiliza ADD para adicionar reclamações ao conjunto de reclamações recebidas. Em Azure AD, isto pode ser configurado como múltiplas transformações de reivindicação.Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicações empresariais.](../develop/active-directory-claims-mapping.md)         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | A declaração de emissão utiliza Expressões Regulares para transformar o valor da reivindicação a emitir.Para obter uma funcionalidade semelhante em Azure AD, pode utilizar uma transformação pré-definida como Extract(), Trim(), ToLower, entre outros. Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicações empresariais.](../develop/active-directory-saml-claims-customization.md)          |

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>Não consigo ver todas as minhas aplicações de AD FS no relatório

 Se instalou a saúde Azure AD Connect, mas ainda vê o pedido para o instalar ou não vê todas as aplicações AD FS no relatório, pode ser que não tenha aplicações AD FS ativas ou as suas aplicações AD FS sejam aplicações da Microsoft.
 
 O relatório de atividade da aplicação AD FS lista todas as aplicações AD FS na sua organização com utilizadores ativos que se inscrevem nos últimos 30 dias. Além disso, o relatório não exibe partes relacionadas com a Microsoft em FS AD como o Office 365. Por exemplo, confiar em festas com o nome 'urn:federation:MicrosoftOnline', 'microsoftonline', 'microsoft:winhello:cert:prov:server' não aparecerá na lista.





## <a name="next-steps"></a>Passos seguintes

- [Vídeo: Como usar o relatório de atividade da AD FS para migrar uma aplicação](https://www.youtube.com/watch?v=OThlTA239lU)
- [Managing applications with Azure Active Directory](what-is-application-management.md) (Gerir aplicações com o Azure Active Directory)
- [Gerir o acesso a aplicações](what-is-access-management.md)
- [Federação Azure AD Connect](../hybrid/how-to-connect-fed-whatis.md)
