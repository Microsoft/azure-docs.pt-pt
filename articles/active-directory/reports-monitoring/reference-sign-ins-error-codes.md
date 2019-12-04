---
title: Códigos de erro no portal de Azure Active Directory | Microsoft Docs
description: Referência aos códigos de erro dos relatórios de atividades de início de sessão.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ed91396dd747bb800d88bc3cd083439a5648624
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786372"
---
# <a name="sign-in-activity-report-error-codes"></a>Códigos de erro do relatório de atividade de entrada 

Com as informações fornecidas pelo [relatório de entradas do usuário](concept-sign-ins.md), você encontra respostas para perguntas como:

- Quem entrou no meu aplicativo?
- Quais aplicativos foram conectados?
- Quais entradas falharam e por quê?

Quando uma entrada falhar, você verá um código de erro correspondente à falha. Este artigo lista os códigos de erro e suas descrições, juntamente com um curso sugerido de ação quando aplicável. 

## <a name="how-can-i-display-failed-sign-ins"></a>Como posso apresentar inícios de sessão com falha? 

Navegue até o [relatório de entradas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) no [portal do Azure](https://portal.azure.com).

![Atividade de entrada](./media/reference-sign-ins-error-codes/61.png "Atividade de início de sessão")

Filtre o relatório para exibir todas as entradas com falha selecionando **falha** na caixa suspensa **status de entrada** .

![Atividade de entrada](./media/reference-sign-ins-error-codes/06.png "Atividade de início de sessão")

A seleção de um item da lista filtrada abre a folha **detalhes da atividade: entradas** . Essa exibição fornece informações adicionais sobre o evento de entrada com falha, incluindo o código de **erro de entrada** e o **motivo da falha**.

![Atividade de entrada](./media/reference-sign-ins-error-codes/05.png "Atividade de início de sessão")

Você também pode acessar os dados de entrada programaticamente usando a [API de relatório](concept-reporting-api.md).

## <a name="error-codes"></a>Códigos de erro


|Erro|Descrição|
|---|---|
|16000|Esse é um detalhe de implementação interna e não uma condição de erro. Você pode ignorar essa referência com segurança.|
|20001|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|20012|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|20033|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|40008|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|40009|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|40014|Há um problema com o seu Fornecedor de Identidade federado. Contacte o seu IDP para resolver este problema.|
|50000|Há um problema no nosso serviço de início de sessão. [Crie um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver este problema.|
|50001|O nome do principal de serviço não foi encontrado neste inquilino. Isso pode acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou se a entidade de recurso não tiver sido encontrada no diretório ou for inválida.|
|50002|O início de sessão falhou devido a acesso limitado ao proxy no inquilino. Se for sua própria política de locatário, você poderá alterar suas configurações de locatário restrito para corrigir esse problema.|
|50003|O início de sessão falhou devido à falta da chave de assinatura ou do certificado. Isto poderá dever-se ao facto de não existir nenhuma chave de assinatura configurada na aplicação. Veja as resoluções descritas em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se o problema persistir, entre em contato com o proprietário do aplicativo ou com o administrador do aplicativo.|
|50005|O usuário tentou fazer logon em um dispositivo de uma plataforma que atualmente não tem suporte por meio da política de acesso condicional.|
|50006| A verificação da assinatura falhou devido a assinatura inválida. Veja a resolução descrita em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Se o problema persistir, contate o proprietário do aplicativo ou o administrador do aplicativo.|
|50007|O certificado de encriptação do parceiro não foi encontrado para esta aplicação. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a Microsoft para corrigir isso.|
|50008|A asserção SAML está em falta ou não está devidamente configurada no token. Contacte o seu fornecedor de federação.|
|50010|A validação do URI de audiência da aplicação falhou, pois não foram configuradas audiências de token. Contate o proprietário do aplicativo para resolução.|
|50011|O endereço de resposta está em falta, está mal configurado ou não corresponde aos endereços de resposta configurados para a aplicação. Experimente a resolução listada em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Se o problema persistir, contate o proprietário do aplicativo ou o administrador do aplicativo.|
|50012| Essa é uma mensagem de erro genérica que indica que a autenticação falhou. Isso pode ocorrer por motivos como credenciais ausentes ou inválidas ou declarações na solicitação. Certifique-se de que a solicitação seja enviada com as credenciais e declarações corretas. |
|50013|A asserção é inválida devido a vários motivos. Por exemplo, o emissor do token não corresponde à versão da API em seu intervalo de tempo válido, o token está expirado ou malformado ou o token de atualização na asserção não é um token de atualização primário.|
|50017|A validação da certificação falhou devido a um dos seguintes motivos:<ul><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>Não foi possível encontrar o CrlSegment esperado</li><li>O certificado da emissora não foi encontrado na lista de certificados fidedignos</li><li>O ponto de distribuição de Delta CRL está configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível obter segmentos de CRL válidos devido a um problema de tempo de ligação excedido</li><li>Não é possível transferir a CRL</li></ul>Contate o administrador do locatário.|
|50020|O usuário não está autorizado por um dos seguintes motivos.<ul><li>O usuário está tentando fazer logon com uma conta MSA com o ponto de extremidade v1</li><li>O usuário não existe no locatário.</li></ul> Contacte o proprietário da aplicação.|
|50027|Token JWT inválido devido aos seguintes motivos:<ul><li>Não contém a afirmação nonce, subafirmação</li><li>erro de correspondência do identificador de requerente</li><li>afirmação duplicada nas afirmações idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro do intervalo de tempo válido </li><li>o formato do token não é adequado</li><li>a verificação de assinatura falhou no token de ID externo do emissor</li></ul>Contacte o proprietário da aplicação.|
|50029|URI inválido: o nome de domínio contém carateres inválidos. Contate o administrador do locatário.|
|50034|O utilizador não existe no diretório. Contate o administrador de locatários.|
|50042|O Salt necessário para gerar um identificador de par está ausente em princípio. Contate o administrador do locatário.|
|50048|O requerente não corresponde à afirmação do Emissor na asserção do cliente. Contate o administrador do locatário.|
|50050|O formato do pedido é incorreto. Contacte o proprietário da aplicação.|
|50053|A conta está bloqueada porque o usuário tentou entrar muitas vezes com uma ID de usuário ou senha incorreta.|
|50055|Palavra-passe inválida; palavra-passe expirada introduzida.|
|50056|Senha inválida ou nula-a senha não existe no repositório para este usuário.|
|50057|A conta de utilizador está desativada. A conta foi desativada por um administrador.|
|50058|A aplicação tentou executar um início de sessão automático e não foi possível iniciar a sessão do utilizador de forma silenciosa. O aplicativo precisa iniciar um fluxo interativo fornecendo aos usuários uma opção para entrar. Contate o proprietário do aplicativo.|
|50059|O utilizador não existe no diretório. Contate o administrador de locatários.|
|50061|O pedido de fim de sessão é inválido. Contacte o proprietário da aplicação.|
|50072|O usuário precisa se registrar para a autenticação de dois fatores (interativo).|
|50074|O utilizador não passou na submissão da MFA.|
|50076|O usuário não passou no desafio de MFA (não interativo).|
|50079|O usuário precisa se registrar para autenticação de dois fatores (logons não interativos).|
|50085|O token de atualização precisa de início de sessão de IDP social. Faça com que o usuário tente entrar novamente com seu nome de usuário e senha.|
|50089|O token de fluxo expirou-falha na autenticação. Fazer com que o usuário tente entrar novamente com seu nome de usuário e senha|
|50097|Autenticação de dispositivo necessária. Isso pode ocorrer porque as declarações DeviceID ou DeviceAltSecId são **nulas**ou se nenhum dispositivo correspondente ao identificador do dispositivo existir.|
|50099|A assinatura JWT é inválida. Contacte o proprietário da aplicação.|
|50105|O utilizador com sessão iniciada não tem uma função atribuída na aplicação em que a sessão está iniciada. Atribua o usuário ao aplicativo. Para obter mais informações: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|O objeto de realm de federação pedido não existe. Contate o administrador do locatário.|
|50120|Emitir com o cabeçalho JWT. Contate o administrador do locatário.|
|50124|A Transformação de Afirmações contém um parâmetro de entrada inválido. Contate o administrador de locatários para atualizar a política.|
|50125|A entrada foi interrompida devido a uma redefinição de senha ou entrada de registro de senha.|
|50126|Nome de usuário ou senha inválido ou nome de usuário ou senha local inválido.|
|50127|O usuário precisa instalar um aplicativo agente para obter acesso a esse conteúdo.|
|50128|Nome de domínio inválido-nenhuma informação de identificação de locatário encontrada na solicitação ou implícita por qualquer credencial fornecida.|
|50129|O dispositivo não é associado ao local de trabalho-o **ingresso no workplace** é necessário para registrar o dispositivo.|
|50130|O valor de declaração não pode ser interpretado como método de autenticação conhecido.|
|50131|Usado em vários erros de acesso condicional. Por exemplo, Mau estado do dispositivo Windows, pedido bloqueado devido a atividades suspeitas, decisões de políticas de acesso e de políticas de segurança.|
|50132|As credenciais foram revogadas devido aos seguintes motivos:<ul><li>O artefacto de SSO é inválido ou expirou</li><li>A sessão não é suficientemente nova para a aplicação</li><li>Foi enviado um início de sessão silencioso, mas a sessão do utilizador com o Azure AD é inválida ou expirou.</li></ul>|
|50133|A sessão é inválida devido à palavra-passe ter expirado ou ter sido alterada recentemente.|
|50135|A alteração de senha é necessária devido ao risco da conta.|
|50136|Redirecione a sessão MSA para o aplicativo-sessão MSA única detectada. |
|50140|Este erro ocorreu devido à interrupção de "Manter sessão iniciada" quando o utilizador estava a iniciar sessão. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o ID de Correlação, o ID do Pedido e o código de Erro para obter mais detalhes. |
|50143|Incompatibilidade de sessão-a sessão é inválida porque o locatário do usuário não corresponde à dica de domínio devido a um recurso diferente.  [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com ID de correlação, ID de solicitação e código de erro para obter mais detalhes.|
|50144|A palavra-passe do Active Directory do utilizador expirou. Gerar uma nova senha para o usuário ou ter o usuário final usando a ferramenta de redefinição de autoatendimento.|
|50146|Esta aplicação tem de ser configurada com uma chave de assinatura específica da aplicação. Ou não está configurada com uma chave destas ou a chave expirou ou ainda não é válida. Contacte o proprietário da aplicação.|
|50148|O code_verifier não corresponde ao code_challenge fornecido no pedido de autorização do PKCE. Contate o desenvolvedor do aplicativo. |
|50155|Falha na autenticação do dispositivo para este usuário.|
|50158|O desafio de segurança externa não foi atendido.|
|50161|As declarações enviadas pelo provedor externo não são suficientes ou estão faltando uma declaração solicitada para o provedor externo.|
|50166|Falha ao enviar solicitação ao provedor de declarações.|
|50169|O realm não é um realm configurado do espaço de nomes do serviço atual.|
|50172|O fornecedor de afirmações externo não foi aprovado. Contate o administrador de locatários|
|50173|É necessário um token de autenticação atualizado. Faça com que o usuário entre novamente usando as novas credenciais.|
|50177|O desafio externo não tem suporte para usuários de passagem.|
|50178|O controle de sessão não tem suporte para usuários de passagem.|
|50180|É necessária a Autenticação Integrada do Windows Ativar o inquilino para SSO Totalmente Integrado.|
|50181|Falha relacionada à OTP durante a entrada. |
|50201|Essa interrupção de prompt de mensagem será mostrada ao usuário durante o logon quando informações adicionais devem ser fornecidas ao usuário.|
|51001|A dica de domínio não está presente no identificador de segurança local-UPN local.|
|51004|A conta de utilizador não existe no diretório.|
|51006|É necessária a Autenticação Integrada do Windows Usuário conectado usando o token de sessão que está faltando por meio de declaração. Pedir ao utilizador que volte a iniciar sessão.|
|52004|O utilizador não deu consentimento para acesso a recursos do LinkedIn. |
|53000|A política de acesso condicional requer um dispositivo em conformidade e o dispositivo não está em conformidade. Faça com que o usuário registre seu dispositivo com um provedor de MDM aprovado, como o Intune.|
|53001|A política de acesso condicional requer um dispositivo associado a um domínio, o que não é o caso deste dispositivo. Fazer com que o usuário use um dispositivo ingressado no domínio.|
|53002|O aplicativo usado não é um aplicativo aprovado para acesso condicional. Para obter acesso, o utilizador tem de utilizar uma das aplicações na lista de aplicações aprovadas.|
|53003|O acesso foi bloqueado devido a políticas de acesso condicional.|
|53004|O utilizador tem de concluir o processo de registo na autenticação multifator antes de aceder a este conteúdo. O utilizador deve registar-se na autenticação multifator.|
|65001|A aplicação X não tem permissão para aceder à aplicação Y ou a permissão foi revogada. Ou o utilizador ou o administrador não permitiu utilizar a aplicação com o ID X. Envie um pedido de autorização interativo para este utilizador e este recurso. Ou o utilizador ou o administrador não permitiu utilizar a aplicação com o ID X. Envie um pedido de autorização ao administrador do seu inquilino para agir em nome da Aplicação :Y para o Recurso : Z.|
|65004|O utilizador recusou dar autorização para aceder à aplicação. Pedir ao utilizador que repita o início de sessão e que autorize o acesso à aplicação|
|65005|A lista de acesso a recursos necessária para a aplicação não contém aplicações detetáveis pelo recurso ou a aplicação cliente pediu acesso a um recurso que não foi especificado nesta lista de acesso a recursos necessária ou o serviço Graph devolveu um pedido inválido ou o recurso não foi encontrado. Se a aplicação suportar SAML, poderá ter configurado a aplicação com o identificador (Entidade) incorreto. Experimente a resolução indicada para SAML através da ligação abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Concessão inválida devido aos seguintes motivos:<ul><li>A asserção de SAML 2.0 pedida tem um Método de Confirmação de Requerente inválido</li><li>O fluxo OnBehalfOf da aplicação não é suportado em V2</li><li>O token de atualização principal não está assinado com a chave de sessão</li><li>Token de atualização externo inválido</li><li>A concessão de acesso foi obtida para outro inquilino.</li></ul>|
|70001|A aplicação com o nome X não foi encontrada no inquilino com o nome Y. Este erro pode acontecer se a aplicação com o identificador X não tiver sido instalada pelo administrador do inquilino ou não tiver sido permitida por qualquer utilizador do inquilino. Você pode ter configurado incorretamente o valor do identificador para o aplicativo ou enviado sua solicitação de autenticação para o locatário incorreto.|
|70002|A aplicação devolveu credenciais de cliente inválidas. Contacte o proprietário da aplicação.|
|70003|A aplicação devolveu um tipo de concessão não suportada. Contacte o proprietário da aplicação.|
|70004|A aplicação devolveu um URI de redirecionamento inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum dos endereços configurados ou a nenhum dos endereços na lista de aprovações OIDC. Contacte o proprietário da aplicação.|
|70005|A aplicação devolveu um tipo de resposta não suportada devido aos seguintes motivos:<ul><li>o tipo de resposta “token” não está ativado na aplicação</li><li>o tipo de resposta “id_token” requer o âmbito “OpenID”; contém um valor de parâmetro de OAuth não suportado no wctx codificado</li></ul>Contacte o proprietário da aplicação.|
|70007|A aplicação devolveu um valor não suportado de “response_mode” durante um pedido de token. Contacte o proprietário da aplicação.|
|70008|O código de autorização ou o token de atualização fornecido expirou ou foi revogado. Faça o usuário tentar entrar novamente.|
|70011|O âmbito que a aplicação pediu é inválido. Contacte o proprietário da aplicação.|
|70012|Ocorreu um erro de servidor ao autenticar um utilizador MSA (consumidor). Repita a entrada e, se o problema persistir, [abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Código de verificação inválido devido ao Utilizador ter introduzido o código de utilizador errado no fluxo de códigos do dispositivo. A autorização não foi aprovada.|
|70019|O código de verificação expirou. Peça ao usuário que tente novamente a entrada.|
|70037|Foi fornecida uma resposta errada para o desafio. A sessão de autenticação remota foi recusada.|
|70043|O gerenciamento de sessão de acesso condicional do Azure força a sessão a expirar|
|70044|O gerenciamento de sessão de acesso condicional do Azure força a sessão a expirar|
|75001|Ocorreu um erro durante o enlace de mensagem SAML.|
|75003|A aplicação devolveu um erro relacionado com o Enlace não suportado (a resposta do protocolo SAML não pode ser enviada através de enlaces que não HTTP POST). Contacte o proprietário da aplicação.|
|75005|O Azure AD não suporta o Pedido SAML enviado pela aplicação para Início de Sessão Único. Contacte o proprietário da aplicação.|
|75008|O pedido da aplicação foi recusado porque o pedido SAML tinha um destino inesperado. Contacte o proprietário da aplicação.|
|75011|O método de autenticação através do qual o utilizador se autenticou no serviço não corresponde ao método de autenticação pedido. Contacte o proprietário da aplicação.|
|75016|O Pedido de Autenticação SAML2 tem uma NameIdPolicy inválida. Contacte o proprietário da aplicação.|
|80001|O Agente de Autenticação não se consegue ligar ao Active Directory. Confirme que o agente de autenticação está instalado num computador associado a um domínio que tenha linha de visão para um controlador de domínio que possa servir o pedido de início de sessão do utilizador.|
|80002|Erro interno. A solicitação de validação de senha atingiu o tempo limite. Não foi possível enviar a solicitação de autenticação para o serviço de identidade híbrida interno. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80003|O Agente de Autenticação recebeu uma resposta inválida. Ocorreu um erro desconhecido ao tentar autenticar no Active Directory no local. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80005|Agente de Autenticação: ocorreu um erro desconhecido ao processar a resposta do Agente de Autenticação. [Abra um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80007|O Agente de Autenticação não conseguiu validar a palavra-passe do utilizador.|
|80010|O Agente de Autenticação não conseguiu desencriptar a palavra-passe. |
|80011|O Agente de Autenticação não conseguiu obter a chave de desencriptação.|
|80012|Os usuários tentaram fazer logon fora dos horários permitidos (isso é especificado no AD).|
|80013|Não foi possível concluir a tentativa de autenticação devido a um desfasamento de horas entre o computador que está a executar o agente de autenticação e o AD. Corrija os problemas de sincronização de hora.|
|80014|O agente de autenticação atingiu o tempo limite. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com o código de erro, ID de correlação e data e hora para obter mais detalhes sobre esse erro.|
|81001|A permissão do Kerberos do utilizador é demasiado grande. Isto pode acontecer se o utilizador estiver em demasiados grupos e, por conseguinte, a permissão do Kerberos contém demasiadas adesões a grupos. Reduza as adesões a grupos do utilizador e tente novamente.|
|81005|Não há suporte para o pacote de autenticação.|
|81007|O locatário não está habilitado para SSO contínuo.|
|81012|Essa não é uma condição de erro. Isso indica que o usuário que está tentando entrar no Azure AD é diferente do usuário conectado ao dispositivo. Você pode ignorar com segurança esse código nos logs.|
|90010|Não há suporte para a solicitação por vários motivos. Por exemplo, a solicitação é feita usando um método de solicitação sem suporte (há suporte apenas para o método POST) ou o algoritmo de assinatura de token que foi solicitado não tem suporte. Contacte o programador da aplicação.|
|90014| Um campo obrigatório para uma mensagem de protocolo estava ausente, contate o proprietário do aplicativo. Se você for o proprietário do aplicativo, verifique se você tem todos os parâmetros necessários para a solicitação de logon. |
|90051| Token de delegação inválido. Foi especificada uma ID de nuvem nacional inválida ({cloudid}).|
|90072| A conta precisa ser adicionada primeiro como um usuário externo no locatário. Saia e entre novamente com uma conta diferente do Azure AD.|
|90094| A concessão requer permissões de administrador. Peça ao seu administrador de locatários para fornecer consentimento para este aplicativo.|
|500011| A entidade de recurso chamada <site address> não foi encontrada no locatário denominado <tenant ID>. Isso pode acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou consentido por qualquer usuário no locatário. Poderá ter enviado o pedido de autenticação para o inquilino errado.|
|500021| O locatário é restrito pelo proxy da empresa. Negando o acesso aos recursos.|
|500121| A autenticação falhou durante uma solicitação de autenticação forte.|
|500133| A asserção não está dentro de seu intervalo de tempo válido. Verifique se o token de acesso não expirou antes de usá-lo para a asserção do usuário ou solicite um novo token.|
|530021|O aplicativo não atende aos requisitos de aplicativo aprovados para acesso condicional.|
|530032|Bloqueado pela política de segurança.| 
|700016|O aplicativo com o identificador ' {appIdentifier} ' não foi encontrado no diretório ' {tenantname} '. Isso pode acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou consentido por qualquer usuário no locatário. Você pode ter enviado sua solicitação de autenticação para o locatário incorreto.|
|900432|Não há suporte para o cliente confidencial na solicitação de nuvem cruzada.|
|7000218|O corpo da solicitação deve conter o seguinte parâmetro: ' client_assertion ' ou ' client_secret '.|


## <a name="next-steps"></a>Passos seguintes

* [Visão geral de relatórios de entradas](concept-sign-ins.md)
* [Acesso programático aos relatórios do Azure AD](concept-reporting-api.md)
