---
title: AD FS insinus in AZure AD with Connect Health | Microsoft Docs
description: Este documento descreve como integrar os sign-ins AD FS com o relatório de ins-ins de conexão Azure AD Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574798"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>AD FS insinus ins in Azure AD with Connect Health - pré-visualização

Os sign-ins AD FS podem agora ser integrados no relatório de inscrições do Azure Ative Directory utilizando a Connect Health. O relatório [de inscrição da Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) inclui informações sobre quando os utilizadores, aplicações e recursos geridos assinam a AZure AD e os recursos de acesso. 

O agente Connect Health for AD FS correlaciona vários IDs de eventos de AD FS, dependentes da versão do servidor, para fornecer informações sobre o pedido e detalhes de erro se o pedido falhar. Estas informações estão relacionadas com o esquema de relatório de inscrição da Azure AD e exibidas no relatório UX Sign-In Azure AD. Paralelamente ao relatório, um novo fluxo de Log Analytics está disponível com os dados AD FS e um novo modelo de livro do Monitor Azure. O modelo pode ser usado e modificado para uma análise aprofundada para cenários como bloqueios de conta AD FS, tentativas de senhas erradas e picos de tentativas inesperadas de entrada.

## <a name="prerequisites"></a>Pré-requisitos
* Azure AD Connect Health para AD FS instalado e atualizado para a versão mais recente.
* Administrador global ou relata função de leitor para ver os sign-ins Azure AD

## <a name="what-data-is-displayed-in-the-report"></a>Quais os dados apresentados no relatório?
Os dados disponíveis espelham os mesmos dados disponíveis para as entradas AZure AD. Cinco separadores com informações estarão disponíveis com base no tipo de súmis, quer Azure AD quer AD FS. A Connect Health correlaciona os eventos a partir de AD FS, dependente da versão do servidor, e corresponde-os ao esquema AD FS. 



#### <a name="user-sign-ins"></a>Insuposições de utilizador 
Cada separador na lâmina de inscrição mostra os valores padrão abaixo:
* Data de início de sessão
* ID do Pedido
* Nome de utilizador ou ID do utilizador
* Estado da inscrição
* Endereço IP do dispositivo utilizado para a inscrição
* Identificador Sign-In

#### <a name="authentication-method-information"></a>Informação do Método de Autenticação
Os seguintes valores podem ser apresentados no separador de autenticação. O método de autenticação é retirado dos registos de auditoria da AD FS.

|Método de autenticação|Description|
|-----|-----|
|Formulários|Nome de utilizador/autenticação de palavra-passe|
|Windows|Autenticação integrada no Windows|
|Certificado|Autenticação com certificados SmartCard / VirtualSmart|
|WindowsHelloForBusiness|Este campo é para auth com Windows Hello for Business. (Autenticação do Passaporte da Microsoft)|
|Dispositivo | Apresentado se a autenticação do dispositivo for selecionada como autenticação "primária" a partir da intranet/extranet e da autenticação do dispositivo.  Não existe autenticação separada do utilizador neste cenário.| 
|Federados|A AD FS não fez a autenticação, mas enviou-a a um fornecedor de identidade de terceiros|
|SSO |Se for utilizado um sinal único, este campo será exibido. Se o SSO tiver um MFA, vai mostrar como Multifactor|
|Multifactor|Se um único sinal de sinal tem um MFA e que foi usado para autenticação, este campo será exibido como Multifactor|
|Azure MFA|AZure MFA é selecionado como Provedor de Autenticação Adicional em FS AD e foi utilizado para autenticação|
|ADFSExternalAuthenticationProvider|Este campo é se um fornecedor de autenticação de terceiros foi registrado e usado para autenticação|


#### <a name="ad-fs-additional-details"></a>Detalhes adicionais do AD FS
Estão disponíveis os seguintes detalhes para os sign-ins da AD FS:
* Server Name (Nome do Servidor)
* Cadeia IP
* Protocolo

### <a name="enabling-log-analytics-and-azure-monitor"></a>Ativar o Log Analytics e o Azure Monitor
O Log Analytics pode ser ativado para os logins AD FS e pode ser utilizado com quaisquer outros componentes integrados do Log Analytics, como o Sentinel.

> [!NOTE] 
> Os logins AD FS podem aumentar o custo do Log Analytics de forma significativa, dependendo da quantidade de logins para AD FS na sua organização. Para ativar e desativar o Log Analytics, selecione a caixa de verificação para o fluxo.

Para ativar o Log Analytics para a funcionalidade, navegue na lâmina 'Log Analytics' e selecione o fluxo "ADFSSignIns". Esta seleção permitirá que os logins AD FS fluam para o Log Analytics.

Para aceder ao modelo atualizado do Livro de Trabalho do Monitor Azure, navegue para "Azure Monitor Templates", e selecione o livro de trabalho "iniciar ins".
Para mais informações sobre os livros de trabalho, visite [os livros de trabalho do Azure Monitor.](https://aka.ms/adfssigninspreview)




### <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes
***Quais são os tipos de inscrições que posso ver?***
O relatório de inscrição apoia os protocolos de inscrição através de O-Auth, WS-Fed, SAML e WS-Trust protocolos. 

***Como são apresentados diferentes tipos de inscrições no relatório de inscrição?***
Se for realizado um sso sem emenda, haverá uma linha para o sinal com um ID de correlação.
Se for realizada uma autenticação de um único fator, duas linhas serão povoadas com o mesmo ID de correlação, mas com dois métodos de autenticação diferentes (isto é, formulários, SSO).
No caso de Autenticação Multi Fator, haverá três linhas com um ID de correlação partilhada e três métodos de autenticação correspondentes (ou seja, Formulários, AzureMFA, Multifactor). Neste exemplo específico, o multifactor neste caso mostra que o SSO tem um MFA.

***Quais são os erros que posso ver no relatório?***
Para obter uma lista completa de erros relacionados com AD FS que são preenchidos no relatório e descrições Sign-In, visite [a referência do código de erro da ajuda da AD FS](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)

***Estou a ver "00000000-0000-0000-0000-00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 O que é que isso significa?***
Se o sinal falhado e a tentativa de UPN não corresponder a uma UPN existente, os campos "User", "Username" e "User ID" serão "000000000-0000-0000-0000-00000-000000000000000000000000000000000000000000000000000000000000000" e o "Sign-in Identificador" será preenchido com o valor tentado que o utilizador erou. Nestes casos, o utilizador que tenta entrar em sing-in não existe.

***Como posso correlacionar os meus eventos no local com o relatório de inscrições da AD Azure?***
O agente Azure AD Connect Health para AD FS correlaciona iDs de eventos de AD FS dependentes da versão do servidor. Os eventos estarão disponíveis no Registo de Segurança dos servidores AD FS. 

***Por que vejo NotSet ou NotApplicable no ID/Nome da aplicação para alguns sign-ins AD FS?***
O Relatório Sign-In AD FS apresentará IDs OAuth no campo de ID de aplicação para insuposições OAuth. Nos cenários de entrada de WS-Trust da WS-Fed, o ID da aplicação será NotSet ou NotApplicable e os identificadores de Recursos IDs e Relying Party estarão presentes no campo de ID de recursos.

***Há mais problemas conhecidos com o relatório na pré-estreia?***
O relatório tem um problema conhecido em que o campo "Requisito de autenticação" no separador "Informações Básicas" será preenchido como um único valor de autenticação de fator para as entradas de FS AD, independentemente da inscrição. Além disso, o separador Detalhes de Autenticação apresentará "Primário ou Secundário" no campo Requirement, com uma correção em curso para diferenciar tipos de autenticação primária ou secundária.


## <a name="related-links"></a>Ligações relacionadas
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Relatório IP arriscado](how-to-connect-health-adfs-risky-ip.md)





