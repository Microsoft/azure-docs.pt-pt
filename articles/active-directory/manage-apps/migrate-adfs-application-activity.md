---
title: Use o relatório de atividade para mover aplicações AD FS para o Diretório Ativo Azure [ Microsoft Docs'
description: O relatório de atividade de aplicação da Ative Directory Federation Services (AD FS) permite migrar rapidamente as aplicações da AD FS para o Azure Ative Directory (Azure AD). Esta ferramenta de migração para AD FS identifica compatibilidade com AD Azure e dá orientação à migração.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978023"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Utilize o relatório de atividade de aplicação da AD FS (pré-visualização) para migrar aplicações para a AD Azure

Muitas organizações usam serviços da Federação de Diretórios Ativos (AD FS) para fornecer um único sinal para aplicações na nuvem. Existem benefícios significativos em transferir as suas aplicações AD FS para a Azure AD para autenticação, especialmente em termos de gestão de custos, gestão de riscos, produtividade, conformidade e governação. Mas compreender quais as aplicações compatíveis com a AD Azure e identificar passos específicos de migração pode ser demorado.

O relatório de atividade de aplicação AD FS (pré-visualização) no portal Azure permite identificar rapidamente quais das suas aplicações são capazes de ser migradas para a AD Azure. Avalia todos os pedidos de compatibilidade da AD FS com a Azure AD, verifica quaisquer problemas e dá orientações sobre a preparação de aplicações individuais para a migração. Com o relatório de atividade de aplicação da AD FS, pode:

* **Descubra as aplicações AD FS e reveja a sua migração.** O relatório de atividade de aplicação da AD FS lista todas as aplicações aD FS na sua organização e indica a sua disponibilidade para a migração para a AD Azure.
* **Priorizar as candidaturas à migração.** Obtenha o número de utilizadores únicos que tenham assinado a aplicação nos últimos 1, 7 ou 30 dias para ajudar a determinar a criticidade ou risco de migração da aplicação.
* **Fazer testes de migração e corrigir problemas.** O serviço de reporte executa automaticamente testes para determinar se uma aplicação está pronta para migrar. Os resultados são apresentados no relatório de atividade de aplicação da AD FS como um estado de migração. Se potenciais problemas de migração forem identificados, obtém orientações específicas sobre como abordar as questões.

Os dados da atividade da aplicação AD FS estão disponíveis para os utilizadores que lhe são atribuídos qualquer uma destas funções de administrador: administrador global, leitor de relatórios, leitor de segurança, administrador de aplicações ou administrador de aplicações na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

* A sua organização deve estar atualmente a usar AD FS para aceder a aplicações.
* A Azure AD Connect Health deve ser ativada no seu inquilino Azure AD.
   * [Saiba mais sobre a Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Começar a criar a Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Descubra aplicações AD FS que podem ser migradas 

O relatório de atividade de aplicação AD FS está disponível no portal Azure no âmbito do Uso de AD Azure & relatórios **de insights.** O relatório de atividade de aplicação da AD FS analisa cada aplicação AD FS para determinar se pode ser migrado como está, ou se é necessário um reexame adicional. 

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma função de administrador que tenha acesso aos dados da atividade de aplicação DaD FS (administrador global, leitor de relatórios, leitor de segurança, administrador de aplicações ou administrador de aplicações na nuvem).

2. Selecione **Diretório Ativo Azure**e, em seguida, selecione **aplicações Enterprise**.

3. No âmbito **da Atividade**, selecione Usage & **Insights (Pré-visualização)** e, em seguida, selecione a atividade de **aplicação aD FS** para abrir uma lista de todas as aplicações AD FS na sua organização.

   ![Atividade de aplicação AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Para cada aplicação na lista de atividades de aplicação da AD FS, consulte o estado de **migração:**

   * **Pronto para migrar** significa que a configuração da aplicação AD FS é totalmente suportada em Azure AD e pode ser migrada como está.

   * A revisão das **necessidades** significa que algumas das configurações da aplicação podem ser migradas para o Azure AD, mas terá de rever as definições que não podem ser migradas como está.

   * **Os passos adicionais necessários** significam que a Azure AD não suporta algumas das definições da aplicação, pelo que a aplicação não pode ser migrada no seu estado atual.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Avaliar a prontidão de um pedido de migração 

1. Na lista de atividades de aplicação da AD FS, clique no estado na coluna do estado da **migração** para abrir detalhes da migração. Verá um resumo dos testes de configuração que passaram, juntamente com quaisquer potenciais problemas de migração.

   ![Detalhes de migração](media/migrate-adfs-application-activity/migration-details.png)

2. Clique numa mensagem para abrir detalhes adicionais da regra da migração. Para obter uma lista completa das propriedades testadas, consulte a tabela de testes de configuração de [aplicação AD FS,](#ad-fs-application-configuration-tests) abaixo.

   ![Detalhes das regras de migração](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Testes de configuração de aplicação AD FS

A tabela seguinte lista todos os testes de configuração que são realizados nas aplicações AD FS.

|Resultado  |Passe/Aviso/Falha  |Descrição  |
|---------|---------|---------|
|Regras de Autenticação Adicional Test-ADFSRP <br> Foi detetada pelo menos uma regra não migratória para a Autenticação Adicional.       | Passe/Aviso          | O partido que depende tem regras para solicitar a autenticação de vários fatores (MFA). Para se mudar para a AD Azure, traduza essas regras em políticas de Acesso Condicional. Se estiver a usar um MFA no local, recomendamos que se mude para o Azure MFA. [Saiba mais sobre acesso condicional.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> O partido de relying tem adicionalWSFedEndpoint definido para verdade.       | Aprovado/Reprovado          | O partido que depende da AD FS permite vários pontos finais de afirmação da WS-Fed.Atualmente, a Azure AD apenas suporta uma.Se tiver um cenário em que este resultado esteja a bloquear a migração, [avise-nos.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> A Parte De Fiação definiu referências permitidas de autenticação.       | Aprovado/Reprovado          | Esta definição em AD FS permite especificar se a aplicação está configurada apenas para permitir certos tipos de autenticação. Recomendamos a utilização do Acesso Condicional para alcançar esta capacidade. Se tiver um cenário em que este resultado esteja a bloquear a migração, [avise-nos.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)  [Saiba mais sobre acesso condicional.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)          |
|Teste-ADFSRP Sempre Requer AAutenticação <br> Sempre requerautenticaçãoCheckResult      | Aprovado/Reprovado          | Esta definição em AD FS permite especificar se a aplicação está configurada para ignorar os cookies SSO e **sempre pedir para autenticação**. Em Azure AD, você pode gerir a sessão de autenticação usando políticas de Acesso Condicional para obter comportamentos semelhantes. Saiba mais sobre a configuração da gestão da sessão de [autenticação com acesso condicional.](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)          |
|Test-ADFSRPAutoUpdateEnabled <br> Relying Party tem conjunto autoUpdateEnabled para verdade       | Passe/Aviso          | Esta definição em AD FS permite especificar se a AD FS está configurada para atualizar automaticamente a aplicação com base em alterações dentro dos metadados da federação. A Azure AD não apoia isso hoje, mas não deve bloquear a migração da candidatura à Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> O Partido De Confiança tem vários ClaimsProviders habilitados       | Aprovado/Reprovado          | Esta definição em AD FS chama a atenção para os fornecedores de identidade a partir dos quais a parte que confia está a aceitar reclamações. Em Azure AD, pode permitir a colaboração externa utilizando o Azure AD B2B. [Saiba mais sobre o Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Regras de Autorização de Delegações test-ADFSRP      | Aprovado/Reprovado          | O pedido tem regras de autorização de delegação personalizadas definidas. Este é um conceito WS-Trust que a Azure AD suporta utilizando protocolos modernos de autenticação, tais como OpenID Connect e OAuth 2.0. [Saiba mais sobre a Plataforma de Identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)          |
|Regras de Autorização de Autorização de Teste-ADFSRPPersonion       | Passe/Aviso          | O pedido tem regras de autorização de personificação personalizada definidas.Este é um conceito WS-Trust que a Azure AD suporta utilizando protocolos modernos de autenticação, tais como OpenID Connect e OAuth 2.0. [Saiba mais sobre a Plataforma de Identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)          |
|Regras de autorização de autorização de teste-ADFSRPIssuance <br> Foi detetada pelo menos uma regra não migratória para autorização de emissão.       | Passe/Aviso          | O pedido tem regras de autorização de emissão personalizadadefinidas em AD FS.A Azure AD suporta esta funcionalidade com acesso condicional Azure AD. [Saiba mais sobre acesso condicional.](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) <br> Também pode restringir o acesso a uma aplicação por utilizador ou grupos atribuídos à aplicação. [Saiba mais sobre a atribuição de utilizadores e grupos para aceder a aplicações.](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)            |
|Test-ADFSRPIssuanceTransformRules <br> Pelo menos uma regra não migratória foi detetada para issuanceTransform.       | Passe/Aviso          | A aplicação tem isevalência personalizada que transforma regras definidas em AD FS. A Azure AD suporta a personalização das reclamações emitidas no símbolo. Para saber mais, consulte [Personalizar as reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).           |
|Test-ADFSRPMonitoringEnabled <br> O Partido De Base tem monitorização Ativado definido para verdade.       | Passe/Aviso          | Esta definição em AD FS permite especificar se a AD FS está configurada para atualizar automaticamente a aplicação com base em alterações dentro dos metadados da federação. A Azure AD não apoia isso hoje, mas não deve bloquear a migração da candidatura à Azure AD.           |
|Teste-ADFSRPNotBeforeskew <br> NotbeforeSkewCheckResult      | Passe/Aviso          | O AD FS permite um desvio de tempo com base nos tempos NotBefore e NotOnOrAfter no token SAML. A AD Azure lida automaticamente com isto por defeito.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> O Partido Fifing tem requestMFAFromClaimsClaimsProviders definidos para verdade.       | Passe/Aviso          | Esta definição em AD FS determina o comportamento do MFA quando o utilizador provém de um fornecedor de reclamações diferente. Em Azure AD, pode permitir a colaboração externa utilizando o Azure AD B2B. Em seguida, pode aplicar políticas de Acesso Condicional para proteger o acesso dos hóspedes. Saiba mais sobre [O Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) e [Acesso Condicional.](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)          |
|Teste-ADFSRPSignedSamlRequestsRequired <br> Partido De Fiação tem AssinadoSamlRequestsRequired definido para verdadeiro       | Aprovado/Reprovado          | A aplicação está configurada em AD FS para verificar a assinatura no pedido SAML. A Azure AD aceita um pedido saml assinado; no entanto, não verificará a assinatura. A Azure AD tem diferentes métodos para proteger contra chamadas maliciosas. Por exemplo, a Azure AD utiliza os URLs de resposta configurados na aplicação para validar o pedido SAML. A Azure AD apenas enviará um símbolo para responder a URLs configurados para a aplicação. Se tiver um cenário em que este resultado esteja a bloquear a migração, [avise-nos.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)          |
|Teste-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Passe/Aviso         | A aplicação está configurada para uma vida útil personalizada. O padrão da AD FS é de uma hora.A Azure AD suporta esta funcionalidade utilizando o Acesso Condicional. Para saber mais, consulte a [gestão da sessão de autenticação Configure com Acesso Condicional.](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)          |
|O Partido De Fiação está preparado para encriptar reclamações. Isto é apoiado pela Azure AD       | Passe          | Com a AD Azure, pode encriptar o token enviado para a aplicação. Para saber mais, consulte a [encriptação token Configure Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|CheckResult encriptado NameIdRequiredCheckResult      | Aprovado/Reprovado          | A aplicação está configurada para encriptar a alegação de identificação de nome no token SAML.Com a AD Azure, pode encriptar todo o token enviado para a aplicação.A encriptação de reclamações específicas ainda não é suportada. Para saber mais, consulte a [encriptação token Configure Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Verifique os resultados dos testes de regra de reclamação

Se configurar uma regra de reclamação para a aplicação em AD FS, a experiência fornecerá uma análise granular para todas as regras de reclamação. Verá quais as regras de reclamação que podem ser transferidas para a AD Azure e quais precisam de ser revistas.

1. Na lista de atividades de aplicação da AD FS, clique no estado na coluna do estado da **migração** para abrir detalhes da migração. Verá um resumo dos testes de configuração que passaram, juntamente com quaisquer potenciais problemas de migração.

2. Na página de detalhes da **regra da Migração,** expanda os resultados para mostrar detalhes sobre potenciais problemas de migração e obter orientação adicional. Para obter uma lista detalhada de todas as regras de reclamação testadas, consulte a tabela [de testes da regra de reclamação,](#check-the-results-of-claim-rule-tests) abaixo.

   O exemplo abaixo mostra detalhes da regra da migração para a regra IssuanceTransform. Ele lista as partes específicas da reivindicação que precisam de ser revistas e abordadas antes de poder migrar a aplicação para a AD Azure.

   ![Regra da migração detalha orientações adicionais](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Ensaios de regra de reclamação

A tabela seguinte enumera todos os testes de regra de reclamação que são realizados em aplicações AD FS.

|Propriedade  |Descrição  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | A declaração de condição utiliza Expressões Regulares para avaliar se a reclamação corresponde a um determinado padrão.Para obter uma funcionalidade semelhante em Azure AD, pode utilizar transformações pré-definidas como IfEmpty(), StartWith(), Contém(), entre outros. Para mais informações, consulte [Personalizar as reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | A declaração de condição tem múltiplas condições que precisam de ser avaliadas antes de executar a declaração de emissão.A Azure AD pode suportar esta funcionalidade com as funções de transformação da reclamação onde pode avaliar vários valores de reclamação.Para mais informações, consulte [Personalizar as reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | A regra da reivindicação não podia ser reconhecida. Para obter mais informações sobre como configurar as reclamações em Azure AD, consulte [Personalizar as reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | A declaração de condição utiliza um Emitente que não é suportado em Azure AD.Atualmente, a Azure AD não tem reclamações de lojas diferentes do Ative Directory ou Azure AD. Se isto o está a impedir de migrar aplicações para a AD Azure, [avise-nos.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)         |
|UNSUPPORTED_CONDITION_FUNCTION      | A declaração de condição utiliza uma função agregada para emitir ou adicionar uma única reclamação, independentemente do número de fósforos.No Azure AD, pode avaliar o atributo de um utilizador para decidir qual o valor a utilizar para a reclamação com funções como IfEmpty(), StartWith(), Contém(), entre outros.Para mais informações, consulte [Personalizar as reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | A declaração de condição usa uma reivindicação restrita em Azure AD. Pode ser capaz de emitir uma reclamação restrita, mas não pode modificar a sua origem ou aplicar qualquer transformação. Para mais informações, consulte [Personalizar as reclamações emitidas em tokens para uma aplicação específica em Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | A declaração de emissão utiliza uma loja de atributos diferente daquela Diretório Ativo. Atualmente, a Azure AD não tem reclamações de lojas diferentes do Ative Directory ou Azure AD. Se este resultado estiver a impedi-lo de migrar aplicações para a AD Azure, [avise-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | A declaração de emissão utiliza add para adicionar reclamações ao conjunto de reclamações de entrada. Em Azure AD, isto pode ser configurado como múltiplas transformações de reclamação.Para mais informações, consulte [Personalizar as reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | A declaração de emissão utiliza Expressões Regulares para transformar o valor da reivindicação a emitir.Para obter uma funcionalidade semelhante em Azure AD, pode utilizar transformação pré-definida como Extrato(), Trim(), ToLower, entre outros. Para mais informações, consulte [Personalizar as reclamações emitidas no token SAML para aplicações empresariais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Passos seguintes

- [Vídeo: Como usar o relatório de atividade da AD FS para migrar uma aplicação](https://www.youtube.com/watch?v=OThlTA239lU)
- [Managing applications with Azure Active Directory](what-is-application-management.md) (Gerir aplicações com o Azure Active Directory)
- [Gerir o acesso a aplicações](what-is-access-management.md)
- [Federação Azure AD Connect](../hybrid/how-to-connect-fed-whatis.md)
