---
title: As definições e dados em roaming perguntas frequentes | Documentos da Microsoft
description: Fornece respostas a algumas perguntas que os administradores de TI podem ter sobre as definições e sincronização de dados de aplicações.
services: active-directory
keywords: Enterprise de estado as configurações de roaming, cloud do windows, perguntas mais frequentes sobre o roaming de estado empresarial
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: a77cf89b7697b7b6b08dead34339ae50dbba8518
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517866"
---
# <a name="settings-and-data-roaming-faq"></a>FAQ de definições e roaming de dados
Este artigo responde a algumas perguntas que os administradores de TI podem ter sobre as definições e sincronização de dados de aplicações.

## <a name="what-data-roams"></a>Os dados que ocorre?
**Definições do Windows**: as definições do PC que são criadas no sistema operacional Windows. Em geral, estas são as definições que personalizar o seu PC, e eles incluem as seguintes categorias amplas:

* *Tema*, que inclui recursos como tema e barra de tarefas das configurações de Desktops.
* *Definições do Internet Explorer*, incluindo separadores foram abertos recentemente e favoritos.
* *Definições do browser Microsoft Edge*, tais como favoritos e lista de leitura.
* *As palavras-passe*, incluindo palavras-passe de Internet, perfis de Wi-Fi e outras pessoas.
* *Preferências de idioma*, que inclui as definições de layouts de teclado, idioma do sistema, data e hora e muito mais.
* *Facilidade de funcionalidades de acesso*, como o tema de alto contraste, o Narrador e Lupa.
* *Outras definições do Windows*, tais como definições de mouse.

**Os dados da aplicação**: Aplicações universais do Windows podem gravar dados de definições para uma pasta de roaming e todos os dados gravados para esta pasta automaticamente serão sincronizados. Cabe-lhe para o desenvolvimento de aplicações individuais como conceber uma aplicação para tirar partido desta capacidade. Para obter mais detalhes sobre como desenvolver uma aplicação Windows Universal, que utiliza o roaming, consulte a [API do armazenamento de appdata](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) e o [appdata do Windows 8 blog de desenvolvedores de roaming](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Que conta é utilizada para sincronização de definições?
No Windows 8.1, a sincronização de definições sempre utilizadas contas da Microsoft de consumidor. Os utilizadores empresariais tinham a capacidade de ligar uma conta Microsoft à conta de domínio do Active Directory para obter acesso à sincronização de definições. No Windows 10, isso ligado funcionalidade está a ser substituída com uma estrutura de conta primária/secundária de conta da Microsoft.

A conta principal é definida como a conta utilizada para iniciar sessão no Windows. Isso pode ser uma conta Microsoft, uma conta do Azure Active Directory (Azure AD), uma conta do Active Directory no local ou uma conta local. Além da conta principal, os utilizadores do Windows 10 podem adicionar uma ou mais contas de cloud secundário para o respetivo dispositivo. Uma conta secundária é, geralmente, uma conta Microsoft, uma conta do Azure AD ou alguma outra conta como o Gmail ou Facebook. Estas contas secundárias fornecem acesso a serviços adicionais, como início de sessão único e a Windows Store, mas eles não têm capacidade de ligar a sincronização de definições.

No Windows 10, apenas a conta principal para o dispositivo pode ser utilizada para sincronização de definições (consulte [como posso atualizar da sincronização de definições de conta Microsoft no Windows 8 para o Azure AD a sincronização de definições no Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Dados nunca é misturar entre as contas de utilizador diferente no dispositivo. Há duas regras de sincronização de definições:

* Definições do Windows sempre irão acompanhá-se a conta principal.
* Dados da aplicação vão ser etiquetados com a conta utilizada para adquirir a aplicação. Apenas aplicações marcadas com a conta principal serão sincronizados. Marcação de propriedade de aplicação é determinado quando um aplicativo está sideload através da Windows Store ou gestão de dispositivos móveis (MDM).

Se não é possível identificar o proprietário de uma aplicação, forem se mover com a conta principal. Se um dispositivo é atualizado do Windows 8 ou Windows 8.1 para o Windows 10, todas as aplicações serão etiquetadas como adquirida pela conta Microsoft. Isto acontece porque a maioria dos usuários adquirir aplicações através da Windows Store, e não havia nenhum suporte a Windows Store para contas do Azure AD antes do Windows 10. Se uma aplicação é instalada através de uma licença offline, a aplicação será marcada utilizando a conta principal do dispositivo.

> [!NOTE]
> Dispositivos Windows 10 pertencentes à empresa e são ligados ao Azure AD já não podem ligar a contas da Microsoft para uma conta de domínio. A capacidade de ligar uma conta Microsoft a uma conta de domínio e ter a sincronização de dados do utilizador para a conta da Microsoft (ou seja, a conta Microsoft roaming através da conta do Microsoft ligada e funções do Active Directory) é removida do Windows 10 dispositivos que estão associados a um ambiente conectado do Active Directory ou do Azure AD.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Como faço a atualização da sincronização de definições de conta Microsoft no Windows 8 para o Azure AD a sincronização de definições no Windows 10?
Se está associados ao domínio do Active Directory, executando o Windows 8.1 com uma conta Microsoft ligada, irá sincronizar as definições através da sua conta Microsoft. Depois de atualizar para o Windows 10, continuará a sincronizar definições de utilizador através da conta Microsoft, desde que seja um utilizador associado a um domínio e domínio do Active Directory não estabelecer ligação com o Azure AD.

Se o domínio do Active Directory no local de se ligar com o Azure AD, o dispositivo irá tentar sincronizar as configurações usando o ligados conta do Azure AD. Se o administrador do Azure AD não ativa o Roaming de estado empresarial, sua ligados conta do Azure AD irá parar a sincronização de definições. Se for um usuário do Windows 10 e iniciar sessão com uma identidade do Azure AD, começa a sincronizar definições do windows, assim que o administrador ativa a sincronização de definições através do Azure AD.

Se tiver armazenado os dados pessoais no seu dispositivo da empresa, deve estar ciente de que os dados de aplicativo e SO de Windows começará a sincronização com o Azure AD. Isto tem as seguintes implicações:

* Definições da sua conta Microsoft pessoas serão levados para além das definições no seu trabalho contas escolares ou profissionais do Azure AD. Isto acontece porque a conta Microsoft e as definições do Azure AD sincronizar agora está a utilizar contas separadas.
* Os dados pessoais, como palavras-passe do Wi-Fi, credenciais de web e os favoritos do Internet Explorer que anteriormente foram sincronizados através de uma conta Microsoft ligada serão sincronizados através do Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Como fazer a conta da Microsoft e de trabalho de interoperabilidade de Roaming de estado do Azure AD Enterprise?
Nas versões de Novembro de 2015 ou posterior do Windows 10, Roaming de estado empresarial só é suportado para uma única conta cada vez. Se inicie sessão no Windows, através de uma conta escola ou profissional do Azure AD, serão sincronizados todos os dados através do Azure AD. Se iniciar sessão utilizando uma conta Microsoft pessoal Windows, todos os dados serão sincronizados através da conta Microsoft. Universal appdata forem se mover utilizando apenas a início de sessão conta principal do dispositivo e forem se mover apenas se a licença da aplicação é de propriedade da conta principal. Appdata universal para as aplicações pertencentes a quaisquer contas secundárias não será sincronizado.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>As definições de sincronização para contas do Azure AD a partir de vários inquilinos?
Quando contas vários Azure AD a partir de diferentes inquilinos do Azure AD estão no mesmo dispositivo, tem de atualizar o registo do dispositivo para comunicar com o serviço Azure Rights Management para cada inquilino do Azure AD.  

1. Localize o GUID para cada inquilino do Azure AD. Abra o portal do Azure e selecione um inquilino do Azure AD. O GUID para o inquilino está na página de propriedades para o inquilino selecionado (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), etiquetadas **ID do diretório**. 
2. Depois de ter o GUID, terá de adicionar a chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<GUID de ID de inquilino >**.
   Do **GUID de ID de inquilino** chave, crie um novo valor de cadeia de caracteres múltipla (REG-MULTI-SZ) denominado **AllowedRMSServerUrls**. Para obter seus dados, especifique os URLs de ponto de distribuição licenciamento dos outros inquilinos do Azure que acessa o dispositivo.
3. Pode encontrar os URLs de ponto de distribuição licenciamento ao executar o **Get-AadrmConfiguration** cmdlet do módulo AADRM. Se os valores para o **LicensingIntranetDistributionPointUrl** e **LicensingExtranetDistributionPointUrl** são diferentes, especifique ambos os valores. Se os valores forem iguais, especifique o valor apenas uma vez.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Quais são as opções de definições de roaming para aplicativos de área de trabalho de Windows existentes?
Roaming só funciona para aplicações Windows universais. Existem duas opções disponíveis para ativar o roaming num aplicativo de área de trabalho existente do Windows:

* O [Bridge de ambiente de trabalho](https://aka.ms/desktopbridge) ajuda a trazer seus aplicativos de desktop do Windows existentes para a plataforma Universal do Windows. A partir daqui, alterações de código mínimo será necessárias para tirar partido de roaming de dados de aplicação do Azure AD. A ponte de ambiente de trabalho fornece as suas aplicações com uma identidade de aplicação, o que é necessária para ativar o roaming para aplicações de ambiente de trabalho existentes de dados de aplicação.
* [Virtualização de experiência do usuário (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) ajuda-o a criar um modelo de definições personalizadas para aplicações de ambiente de trabalho de Windows existentes e ativar o roaming para aplicações de Win32. Esta opção não necessita de programador da aplicação alterar o código da aplicação. UE-V está limitado a roaming de Active Directory no local para os clientes que compraram o Microsoft Desktop Optimization Pack.

Os administradores podem configurar a UE-V para se mover dados de aplicativo de desktop do Windows, alterando o roaming das configurações de SO Windows e os dados de aplicação Universal através de [políticas de grupo da UE-V](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), incluindo:

* Se mover a diretiva de grupo de definições do Windows
* Não sincronizar a diretiva de grupo de aplicações do Windows
* Internet Explorer em roaming na secção de aplicações

No futuro, a Microsoft pode investigar maneiras de tornar o UE-V profundamente integrada ao Windows e estender o UE-V para se mover as definições através da cloud do Azure AD.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Posso armazenar definições sincronizadas e os dados no local?
Roaming de estado empresarial armazena todos os dados de sincronização na cloud da Microsoft. UE-V oferece uma local roaming de solução.

## <a name="who-owns-the-data-thats-being-roamed"></a>Quem tem os dados que estão a ser movidos?
As empresas próprias ganhar mobilidade os dados por meio de Roaming de estado empresarial. Dados são armazenados num datacenter do Azure. Todos os dados de utilizador são encriptados em trânsito e em inatividade na cloud com o serviço Azure Rights Management do Azure Information Protection. Essa é uma melhoria em comparação comparada a sincronização de definições com base na conta da Microsoft, que criptografa apenas determinados dados confidenciais, como credenciais de utilizador, antes mesmo de sair do dispositivo.

A Microsoft está empenhada em proteger dados do cliente. Dados de definições de um utilizador de enterprise são encriptados automaticamente pelo serviço Azure Rights Management antes mesmo de sair um dispositivo Windows 10, para que nenhum outro utilizador pode ler esses dados. Se a sua organização tiver uma subscrição paga para o serviço Azure Rights Management, pode utilizar outras funcionalidades de proteção, como controlar e revogar documentos, automaticamente proteger os e-mails que contêm informações confidenciais e gerir as suas próprias chaves (o "traga a sua própria chave"solução, também conhecida como BYOK). Para obter mais informações sobre estas funcionalidades e como funciona este serviço de proteção, consulte [o que é o Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Pode gerir a sincronização para uma aplicação específica ou a definição?
No Windows 10, não existe qualquer definição de diretiva de grupo ou de MDM para desativar o roaming para uma aplicação individual. Os administradores de inquilinos podem desativar a sincronização de appdata para todas as aplicações num dispositivo gerido, mas não existe nenhum um melhor controle a um nível por aplicação ou numa aplicação.

## <a name="how-can-i-enable-or-disable-roaming"></a>Como ativar ou desativar o roaming?
Na **definições** aplicação, aceda à **contas** > **sincronizar as definições**. Nesta página, pode ver que conta está a ser utilizada para as definições de roaming e pode ativar ou desativar a grupos individuais de configurações para ganhar mobilidade.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>O que é recomendação da Microsoft para ativar o roaming no Windows 10?
A Microsoft tem algumas diferentes configurações de roaming soluções disponíveis, incluindo perfis de utilizador itinerantes, UE-V e o Roaming de estado do Enterprise.  A Microsoft está empenhada em tornar um investimento em Enterprise Roaming em futuras versões do Windows de estado. Se sua organização não estiver pronto ou à vontade com mover dados para a cloud, em seguida, recomendamos que utilize o UE-V como sua principal tecnologia de roaming. Se sua organização necessita de suporte para aplicativos de área de trabalho de Windows existentes de roaming, mas é avançar para a cloud, recomendamos que utilize o Roaming de estado do Enterprise e UE-V. Embora o UE-V e o Roaming de estado empresarial são tecnologias muito semelhantes, não são mutuamente exclusivas. Se complementam entre si para ajudar a garantir que sua organização fornece os serviços móveis que os utilizadores precisam.  

Ao utilizar o Roaming de estado empresarial e o UE-V, aplicam as seguintes regras:

* Roaming de estado empresarial é o principal agente roaming no dispositivo. UE-V está a ser utilizado para complementar a "lacuna de Win32".
* Efetuar o roaming para as definições do Windows e dados de aplicação UWP modernos de UE-V deve ser desativado quando o grupo de UE-V utilizando as políticas. Elas já estão cobertas pelas Roaming de estado empresarial.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Como o Roaming de estado de Enterprise suporta a infraestrutura de ambiente de trabalho virtual (VDI)?
Roaming de estado empresarial é suportada em SKUs de cliente do Windows 10, mas não no servidor SKUs. Se uma VM do cliente está hospedado numa máquina de hipervisor e iniciar sessão remotamente na máquina virtual, os dados forem se mover. Se vários usuários compartilham o mesmo sistema operacional e os utilizadores iniciam sessão remotamente num servidor para uma experiência de ambiente de trabalho completo, roaming poderá não funcionar. O último cenário com base em sessão não é oficialmente suportado.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>O que acontece quando a minha organização compra uma subscrição que inclui o Azure Rights Management depois de utilizar o roaming?
Se sua organização já utiliza o roaming no Windows 10 com a subscrição gratuita do Azure Rights Management utilização limitada, compra um [subscrição paga](https://azure.microsoft.com/pricing/details/information-protection/) que inclui o serviço de proteção não terão o Azure Rights Management qualquer impacto na funcionalidade da funcionalidade de roaming e sem alterações de configuração necessárias para o administrador de TI.

## <a name="known-issues"></a>Problemas conhecidos
Consulte a documentação sobre o [resolução de problemas](enterprise-state-roaming-troubleshooting.md) secção para obter uma lista dos problemas conhecidos. 

## <a name="next-steps"></a>Passos Seguintes 

Para uma descrição geral, consulte [descrição geral de roaming de estado empresarial](enterprise-state-roaming-overview.md)
