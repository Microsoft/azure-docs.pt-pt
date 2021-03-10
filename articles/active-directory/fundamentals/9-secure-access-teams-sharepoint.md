---
title: Acesso externo seguro a Microsoft Teams, SharePoint e OneDrive com Azure Ative Directory
description: Acesso seguro aos serviços da Microsoft 365 como parte da sua segurança de acesso externo geral.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f373c849cd542769fec7a10f20dad04e742c9591
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565143"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Acesso externo seguro a Microsoft Teams, SharePoint e OneDrive para negócios 

Microsoft Teams, SharePoint e OneDrive for Business são três das formas mais utilizadas de colaborar e partilhar conteúdos com utilizadores externos. Se os métodos "aprovados" forem demasiado restritivos, os utilizadores ficarão fora dos métodos aprovados enviando e-mails de conteúdo ou criando processos e aplicações externas inseguras, como um DropBox pessoal ou OneDrive. O seu objetivo é equilibrar as suas necessidades de segurança com facilidade de colaboração.

Este artigo guia-o a determinar e configurar a colaboração externa para cumprir os seus objetivos de negócio usando a Microsoft Teams e a SharePoint.

## <a name="governance-begins-in-azure-active-directory"></a>Governação começa no Azure Ative Directory

A partilha na Microsoft 365 é em parte regida pelas [identidades externas | Definições de colaboração externa](https://aad.portal.azure.com/) no Azure Ative Directory (Azure AD). Se a partilha externa for desativada ou restringida no Azure AD, substitui quaisquer definições de partilha configuradas no Microsoft 365. Uma exceção a isto é que se a integração AZure AD B2B não estiver ativada, o SharePoint e o OneDrive podem ser configurados para suportar a partilha ad-hoc através de códigos de acesso únicos (OTP).

![Screenshot das definições de colaboração externa](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>Acesso de utilizador convidado

Existem três opções para o acesso ao utilizador convidado, que controla o que os utilizadores dos hóspedes podem ver depois de serem convidados. 

Para evitar que os utilizadores convidados vejam detalhes de outros utilizadores convidados, e sejam capazes de enumerar a adesão ao grupo, escolher os utilizadores do Guest têm acesso limitado a propriedades e membros de objetos de diretório.

### <a name="guest-invite-settings"></a>Configurações de convite para convidados

Estas configurações determinam quem pode convidar os hóspedes e como esses hóspedes podem ser convidados. Estas definições só são ativadas se a integração com o B2B estiver ativada.

Recomendamos que os administradores e utilizadores na função de convidado convidado possam convidar. Esta definição permite a configuração de processos de colaboração controlados, como no exemplo seguinte.

* O dono da equipa submete um bilhete para ser atribuído o papel de convidado convidado, e

   * Torna-se responsável por todos os convites de hóspedes.

   * Concorda em não adicionar diretamente os utilizadores ao SharePoint subjacente

   * É responsável para realizar revisões de acesso regulares e revogar o acesso conforme apropriado.

* Central IT faz o seguinte

   * Permite a partilha externa, concedendo o papel solicitado após a conclusão da formação.

   * Atribui licença Azure AD P2 ao proprietário do grupo Microsoft 365 para permitir comentários de acesso.
   * Cria uma revisão de acesso ao grupo Microsoft 365.

   * Confirma que estão a ocorrer comentários de acesso.

   * Remove os utilizadores diretamente adicionados ao SharePoint subjacente.

 **Detiveter o Email Códigos de Acesso únicos para os hóspedes (Pré-visualização) e Ative o sinal de autosserviço do hóspede através dos fluxos do utilizador** para **sim**. Esta definição tira partido da integração com as definições de colaboração externa Azure AD.

### <a name="collaboration-restrictions"></a>Restrições de colaboração

Há três opções sob restrições de colaboração. Os seus requisitos de negócio ditam o que vai escolher.

* **Permitir que os convites sejam enviados para qualquer domínio** significa que qualquer utilizador pode ser convidado a colaborar.

* **Negar convites para os domínios especificados** significa que qualquer utilizador fora deles pode ser convidado a colaborar.

* **Permitir convites apenas para os domínios especificados** significa que qualquer utilizador fora dos domínios especificados não pode ser convidado. 

## <a name="govern-access-in-teams"></a>Governar o acesso em Equipas

[As equipas diferenciam utilizadores externos (qualquer pessoa fora da sua organização) e utilizadores convidados (aqueles com contas de hóspedes).](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b) Gere a definição de colaboração no [portal Teams Admin](https://admin.teams.microsoft.com/company-wide-settings/external-communications) em definições org-wide. 

> [!NOTE]
> As definições de colaboração de identidades externas no Azure Ative Directory controlam as permissões eficazes. Pode aumentar as restrições em Equipas, mas não diminuí-las do que está definido no Azure AD.

* **Definições de Acesso Externo**. Por padrão, as equipas permitem o acesso externo, o que significa que a organização pode comunicar com todos os domínios externos. Se quiser restringir ou permitir domínios específicos apenas para equipas, pode fazê-lo aqui.

* **Acesso ao Hóspede**. O acesso ao hóspede controla o que os utilizadores convidados podem fazer em equipas.

Para saber mais sobre a gestão do acesso externo em Equipas, consulte os seguintes recursos.

* [Gerir o acesso externo em Equipas microsoft](/microsoftteams/manage-external-access)

* [Microsoft 365 modelos de identidade e Azure Ative Directory](/microsoft-365/enterprise/about-microsoft-365-identity)

* [Modelos de identidade e autenticação para equipas da Microsoft](/MicrosoftTeams/identify-models-authentication)

* [Etiquetas de sensibilidade para equipas da Microsoft](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>Reger o acesso no SharePoint e no OneDrive

Os administradores do SharePoint têm muitas configurações disponíveis para colaboração. As configurações em toda a organização são geridas a partir do centro de administração sharePoint. As definições podem ser ajustadas para cada site do SharePoint. Recomendamos que as configurações em toda a organização estejam nos níveis mínimos de segurança necessários e que aumente a segurança em sites específicos, conforme necessário. Por exemplo, para um projeto de alto risco, você pode querer restringir os utilizadores a determinados domínios, e desativar a capacidade dos membros de convidar os hóspedes.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>Integração do SharePoint e unidade única com a Azure AD B2B

Como parte da sua estratégia global para governar a colaboração externa, recomendamos que [permita a pré-visualização do SharePoint e a integração oneDrive com a Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview) .

O Azure AD B2B fornece autenticação e gestão de utilizadores convidados. Com a integração do SharePoint e do OneDrive, as [senhas únicas do Azure AD B2B](../external-identities/one-time-passcode.md) são utilizadas para a partilha externa de ficheiros, pastas, itens de lista, bibliotecas de documentos e sites. Esta funcionalidade proporciona uma experiência atualizada a partir da [experiência do destinatário de partilha externa seguro](/sharepoint/what-s-new-in-sharing-in-targeted-release)existente.

> [!NOTE]
> Se ativar a pré-visualização da integração AZure AD B2B, então a partilha do SharePoint e da OneDrive está sujeita às definições de relações organizacionais AD AZure, tais como **membros podem convidar** e **os hóspedes podem convidar.**

### <a name="sharing-policies"></a>Políticas de partilha

*A partilha externa* pode ser definida tanto para o SharePoint como para o OneDrive. As restrições OneDrive não podem ser mais permissivas do que as definições do SharePoint.

 ![Screenshot de definições de partilha externa no SharePoint e NomDrive](media/secure-external-access/9-sharepoint-settings.png)

A integração do SharePoint com o Azure AD B2B altera a forma como os controlos interagem com as contas.

* **Qualquer um.** Não recomendado

   * Independentemente do estado de integração, permitir que qualquer pessoa ligue significa que nenhuma política de Azure será aplicada quando este tipo de ligação é usado. 

   * Num cenário de colaboração governada, não permita esta funcionalidade. 
   > [!NOTE]
   > Você pode encontrar um cenário onde você precisa ativar esta configuração para um site específico, nesse caso você iria capacitá-lo aqui, e definir a maior restrição em sites individuais.

* **Convidados novos e existentes.** Recomendado se tiver integração ativada.

   * **Com a integração AZure AD B2B** ativada, os novos e existentes hóspedes terão uma conta de hóspedes Azure AD B2B que pode ser gerida com políticas AD AD do Azure.

   * **Sem a integração AD B2B** ativada, os novos hóspedes não terão uma conta AD B2B criada, e não podem ser geridos a partir de Azure AD. Se os hóspedes existentes têm uma conta Azure AD B2B depende de como o hóspede foi criado.

* **Hóspedes existentes.** Recomendado se não tiver integração ativada.

   * Com isto ativado, os utilizadores só podem partilhar com outros utilizadores já no seu diretório.

* **Apenas pessoas na sua organização.** Não é recomendado quando precisa de colaborar com utilizadores externos.

   * Independentemente do estado de integração, os utilizadores só poderão partilhar com os utilizadores da sua organização. 

* **Limitar a partilha externa por domínio.** Por predefinição, o SharePoint permite o acesso externo, o que significa que a partilha é permitida com todos os domínios externos. Se quiser restringir ou permitir domínios específicos apenas para o SharePoint, pode fazê-lo aqui.

* **Permitir que apenas utilizadores em grupos de segurança específicos partilhem externamente**.  Esta definição restringe quem pode partilhar conteúdo no SharePoint e no OneDrive, enquanto a definição em Azure AD se aplica a todas as aplicações. Restringir quem pode partilhar pode ser útil se quiser exigir que os seus utilizadores façam uma formação sobre partilha de forma segura, e, em conclusão, adicione-os a um grupo de segurança de partilha aprovado. Se esta definição for selecionada e os utilizadores não tiverem uma forma de aceder a ser um "sharer aprovado", poderão, em vez disso, encontrar formas não aprovadas de partilhar. 

* **Permitir que os hóspedes partilhem itens que não possuem.** Recomendamos deixar este deficiente.

* **As pessoas que usam um código de verificação devem reautenticar-se após tantos dias (o padrão é 30)**. Recomendamos ativar esta definição.

### <a name="access-controls"></a>Controlos de acesso

A definição de controlos de acesso afetará todos os utilizadores da sua organização. Dado que pode não ser capaz de controlar se os utilizadores externos têm dispositivos compatíveis, não abordaremos esses controlos aqui. 

* **Sessão ociosa.** Recomendamos que ative este controlo, que lhe permite alertar e inscrever utilizadores em dispositivos não geridos após um período de inatividade. Pode configurar o período de inatividade e o aviso. 

* **Localização da rede**. Definir este controlo significa que pode permitir o acesso apenas a endereços IP de formulário que a sua organização possui. Em cenários de colaboração externa, desaça-o apenas se todos os seus parceiros externos acederem a recursos apenas dentro da sua rede, ou através da sua VPN.

### <a name="file-and-folder-links"></a>Links de ficheiros e pastas

No centro de administração do SharePoint, também pode definir como os links de ficheiros e pastas são partilhados. Também pode configurar estas definições para cada site. 

 ![Screenshot das definições de ligação de ficheiros e pastas](media/secure-external-access/9-file-folder-links.png)

Se tiver ativado a integração com o Azure AD B2B, a partilha de ficheiros e pastas com os que estão fora da organização resultará na criação de um utilizador B2B quando os ficheiros e a pasta são partilhados.

Recomendamos a definição do tipo de ligação predefinido para **apenas pessoas da sua organização,** e permissões por defeito para **Editar**. Ao fazê-lo, garante que os itens são partilhados de forma ponderada. Em seguida, pode personalizar esta definição para o padrão por site que atendam às necessidades específicas de colaboração.

### <a name="anyone-links"></a>Qualquer um liga

Não recomendamos permitir ligações a ninguém. Se o fizer, recomendamos a definição de uma expiração e consideramos restringi-las para visualizar permissões. Se escolher Apenas permissões de Ver para ficheiros ou pastas, os utilizadores não poderão alterar as ligações de Ninguém para incluir privilégios de edição.

Para saber mais sobre a governação do acesso externo ao SharePoint consulte o seguinte:

* [Visão geral de partilha externa do SharePoint](/sharepoint/external-sharing-overview)

* [Integração sharePoint e OneDrive com Azure AD B2B](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos sobre a garantia do acesso externo aos recursos. Recomendamos que tome as ações na ordem listada.

1. [Determine a sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descubra o seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governação](3-secure-access-plan.md)

4. [Utilize grupos para segurança](4-secure-access-groups.md)

5. [Transição para Azure AD B2B](5-secure-access-b2b.md)

6. [Acesso seguro com Gestão de Direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com etiquetas de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Acesso seguro a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md) (Está aqui.)