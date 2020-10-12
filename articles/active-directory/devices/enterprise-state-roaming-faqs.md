---
title: Enterprise State Roaming FAQ - Azure Ative Directory
description: Perguntas frequentes sobre ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9510bd564ced2f458a9a78ff23200bb32358c3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268541"
---
# <a name="settings-and-data-roaming-faq"></a>FAQ de definições e roaming de dados

Este artigo responde a algumas perguntas que os administradores de TI podem ter sobre configurações e sincronização de dados de aplicações.

## <a name="what-data-roams"></a>Que dados vagueiam?

**Definições do Windows**: as definições para PC que são incorporadas no sistema operativo Windows. Geralmente, estas são configurações que personalizam o seu PC, e incluem as seguintes grandes categorias:

* *Tema*, que inclui funcionalidades como o tema do ambiente de trabalho e as definições da barra de tarefas.
* *Definições do Internet Explorer*, que incluem os favoritos e os separadores recentemente abertos.
* *Definições do navegador Microsoft Edge*, como favoritos e lista de leitura.
* *Palavras-passe*, incluindo palavras-passe da Internet, perfis de Wi-Fi, entre outros.
* *Preferências de idioma*, que incluem definições relativas a esquemas de teclado, ao idioma do sistema, à data e hora e mais.
* *Funcionalidades de facilidade de acesso*, tais como o tema de alto contraste, o Narrador e a Lupa.
* *Outras definições do Windows*, tais como as definições do rato.

> [!NOTE]
> Este artigo aplica-se ao navegador baseado em HTML do Microsoft Edge Legacy lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Chromium microsoft Edge lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

**Dados da aplicação**: As aplicações universais do Windows podem escrever dados de definições para uma pasta de roaming, e quaisquer dados escritos nesta pasta serão automaticamente sincronizados. Cabe ao programador de aplicações individual conceber uma app para tirar partido desta capacidade. Para obter mais informações sobre como desenvolver uma aplicação Universal Windows que utiliza roaming, consulte a [API de armazenamento de dados de aplicações](/windows/uwp/design/app-settings/store-and-retrieve-app-data) e o [blog de desenvolvimento de roaming de aplicativos do Windows 8.](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/)

## <a name="what-account-is-used-for-settings-sync"></a>Que conta é utilizada para sincronização de definições?

No Windows 8.1, as definições sincronizam sempre as contas da Microsoft do consumidor. Os utilizadores da empresa tinham a capacidade de ligar uma conta Microsoft à sua conta de domínio ative Directory para terem acesso a sincronização de definições. No Windows 10, esta funcionalidade de conta microsoft conectada está a ser substituída por um quadro de conta primária/secundária.

A conta principal é definida como a conta utilizada para iniciar scontabilidade no Windows. Esta pode ser uma conta Microsoft, uma conta Azure Ative Directory (Azure AD), uma conta de Ative Directory no local ou uma conta local. Além da conta principal, os utilizadores do Windows 10 podem adicionar uma ou mais contas secundárias na nuvem ao seu dispositivo. Uma conta secundária é geralmente uma conta Microsoft, uma conta AD Azure ou outra conta, como o Gmail ou o Facebook. Estas contas secundárias fornecem acesso a serviços adicionais, como o single sign-on e a Windows Store, mas não são capazes de alimentar as definições sincronizadas.

No Windows 10, apenas a conta primária do dispositivo pode ser utilizada para sincronização de definições (ver como faço o [upgrade das definições de conta da Microsoft sincronizar nas definições de AD do Windows 8 para Azure?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)

Os dados nunca são misturados entre as diferentes contas de utilizador do dispositivo. Existem duas regras para sincronização de definições:

* As definições do Windows irão sempre circular com a conta principal.
* Os dados da aplicação serão marcados com a conta utilizada para adquirir a app. Apenas as aplicações marcadas com a conta principal sincronizarão. A marcação de propriedade da aplicação é determinada quando uma aplicação é carregada lateralmente através da Windows Store ou da gestão de dispositivos móveis (MDM).

Se o proprietário de uma aplicação não puder ser identificado, irá vaguear com a conta principal. Se um dispositivo for atualizado do Windows 8 ou Windows 8.1 para o Windows 10, todas as aplicações serão marcadas como adquiridas pela conta microsoft. Isto porque a maioria dos utilizadores adquire aplicações através da Windows Store, e não existia suporte à Windows Store para contas AD do Azure antes do Windows 10. Se uma aplicação for instalada através de uma licença offline, a aplicação será marcada usando a conta principal do dispositivo.

> [!NOTE]
> Os dispositivos windows 10 que são propriedade da empresa e estão ligados ao Azure AD já não podem ligar as suas contas microsoft a uma conta de domínio. A capacidade de ligar uma conta Microsoft a uma conta de domínio e ter toda a sincronização de dados do utilizador na conta da Microsoft (isto é, a conta microsoft a roaming através da conta conectada da Microsoft e da funcionalidade Ative Directory) é removida dos dispositivos windows 10 que se juntam a um ambiente de Ative Directory ou AD ligado.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Como faço o upgrade das definições de conta da Microsoft sincronizadas no Windows 8 para as definições de AD do Azure sincronizadas no Windows 10?

Se estiver ligado ao domínio do Ative Directory que executa o Windows 8.1 com uma conta Microsoft conectada, sincronizará as definições através da sua conta Microsoft. Após a atualização para o Windows 10, continuará a sincronizar as definições do utilizador através da conta microsoft, desde que seja um utilizador ligado ao domínio e o domínio ative directory não se conecte com a Azure AD.

Se o domínio ative directy se ligar ao Azure AD, o seu dispositivo tentará sincronizar as definições utilizando a conta AD Azure conectada. Se o administrador AD da Azure não ativar o Roaming do Estado da Empresa, a sua conta AZure AD ligada deixará de sincronizar as definições. Se for um utilizador do Windows 10 e iniciar sôs com uma identidade AD Azure, começará a sincronizar as definições do Windows assim que o seu administrador ativar as definições sincronizadas através do Azure AD.

Se armazenar quaisquer dados pessoais no seu dispositivo corporativo, deve estar ciente de que o Windows OS e os dados da aplicação começarão a sincronizar com o Azure AD. Isto tem as seguintes implicações:

* As definições pessoais da sua conta Microsoft afastar-se-ão das definições do seu trabalho ou das contas AZure AD da escola. Isto porque a conta microsoft e as definições AD do Azure estão agora a utilizar contas separadas.
* Dados pessoais como Wi-Fi palavras-passe, credenciais web e favoritos do Internet Explorer que foram previamente sincronizados através de uma conta da Microsoft conectada serão sincronizados através do Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Como funcionam as contas da Microsoft e da interoperabilidade do Roaming do Estado da Azure AD Enterprise?

Nos lançamentos de novembro de 2015 ou posteriores do Windows 10, o Enterprise State Roaming só é suportado por uma única conta de cada vez. Se iniciar seduca no Windows utilizando uma conta AD Azure de trabalho ou escola, todos os dados sincronizarão através do Azure AD. Se iniciar sôs o teste ao Windows utilizando uma conta pessoal da Microsoft, todos os dados sincronizarão através da conta Microsoft. Os dados de aplicações universais irão circular utilizando apenas a conta de entrada primária no dispositivo, e só vaguearão se a licença da aplicação for detida pela conta principal. Os dados universais das aplicações detidas por quaisquer contas secundárias não serão sincronizados.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>As configurações sincronizam as contas AD da Azure de vários inquilinos?

Quando várias contas AD da Azure de diferentes inquilinos da AD Azure estiverem no mesmo dispositivo, você deve atualizar o registo do dispositivo para comunicar com o serviço de Gestão de Direitos Azure para cada inquilino AZure AD.  

1. Encontre o GUID para cada inquilino AZure AD. Abra o portal Azure e selecione um inquilino AZure AD. O GUID para o arrendatário está na página Propriedades para o inquilino selecionado https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) (, rotulada **ID do Diretório**. 
2. Depois de ter o GUID, terá de adicionar a chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID> **.
   A partir da chave **ID GUID** do inquilino, crie um novo valor multi-string (REG-MULTI-SZ) denominado **AllowedRMSServerUrls**. Para os seus dados, especifique os URLs do ponto de distribuição de licenciamento dos outros inquilinos da Azure a que o dispositivo acede.
3. Pode encontrar os URLs do ponto de distribuição de licenciamento executando o cmdlet **Get-AadrmConfiguration** a partir do módulo AADRM. Se os valores para o **LicenciamentoIntranetDistributionPointUrl** e **LicensingExtranetDistributionPointUrl** forem diferentes, especifique ambos os valores. Se os valores forem os mesmos, especifique o valor apenas uma vez.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Quais são as opções de definições de roaming para aplicações de ambiente de trabalho existentes no Windows?

Roaming só funciona para aplicações Universal Windows. Existem duas opções disponíveis para permitir o roaming numa aplicação de ambiente de trabalho do Windows existente:

* A [Ponte desktop](https://aka.ms/desktopbridge) ajuda-o a trazer as suas aplicações de ambiente de trabalho windows existentes para a Plataforma Universal windows. A partir daqui, serão necessárias alterações mínimas de código para tirar partido dos dados da aplicação AD do Azure. O Desktop Bridge fornece às suas aplicações uma identidade de aplicação, que é necessária para permitir o roaming de dados de aplicações para aplicações de ambiente de trabalho existentes.
* [A Virtualização da Experiência do Utilizador (UE-V) ajuda-o](/previous-versions//dn458947(v=vs.85)) a criar um modelo de definições personalizadas para aplicações de ambiente de trabalho do Windows existentes e a ativar o roaming para aplicações Win32. Esta opção não requer que o desenvolvedor de aplicações altere o código da aplicação. A UE-V está limitada ao roaming ativo do Ative Directory para clientes que tenham adquirido o Microsoft Desktop Optimization Pack.

Os administradores podem configurar UE-V para alterar os dados das aplicações de ambiente de trabalho do Windows, alterando o roaming das definições do Windows OS e os dados das aplicações universais através das [políticas do grupo UE-V,](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)incluindo:

* Política de grupo de configurações roam Windows
* Não sincronize a política do grupo Windows Apps
* Internet Explorer roaming na secção de aplicações

No futuro, a Microsoft poderá investigar formas de tornar a UE-V profundamente integrada no Windows e estender a UE-V às definições de roam através da nuvem AD Azure.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Posso armazenar configurações sincronizadas e dados no local?

Enterprise State Roaming armazena todos os dados sincronizados na nuvem da Microsoft. A UE-V oferece uma solução de roaming no local.

## <a name="who-owns-the-data-thats-being-roamed"></a>Quem é o dono dos dados que estão a ser vagueados?

As empresas são proprietárias dos dados percorridos através do Roaming do Estado da Empresa. Os dados são armazenados num datacenter Azure. Todos os dados do utilizador são encriptados tanto em trânsito como em repouso na nuvem utilizando o serviço de Gestão de Direitos Azure da Azure Information Protection. Esta é uma melhoria em comparação com a sincronização de definições baseadas na microsoft, que encripta apenas certos dados sensíveis, como credenciais de utilizador antes de deixar o dispositivo.

A Microsoft está empenhada em salvaguardar os dados dos clientes. Os dados de configurações de um utilizador da empresa são automaticamente encriptados pelo serviço Azure Rights Management antes de deixar um dispositivo Windows 10, pelo que nenhum outro utilizador pode ler estes dados. Se a sua organização tiver uma subscrição paga para o serviço de Gestão de Direitos Azure, pode utilizar outras funcionalidades de proteção, como rastrear e revogar documentos, proteger automaticamente e-mails que contenham informações sensíveis e gerir as suas próprias chaves (a solução "traga a sua própria chave", também conhecida como BYOK). Para obter mais informações sobre estas funcionalidades e como este serviço de proteção funciona, consulte [O que é Azure Rights Management.](/azure/information-protection/what-is-information-protection)

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Posso gerir a sincronização para uma aplicação ou configuração específica?

No Windows 10, não existe uma definição de MDM ou Política de Grupo para desativar o roaming para uma aplicação individual. Os administradores de inquilinos podem desativar o sincronização de appdatas para todas as aplicações num dispositivo gerido, mas não existe um controlo mais fino a nível de uma aplicação por aplicação ou dentro da app.

## <a name="how-can-i-enable-or-disable-roaming"></a>Como posso permitir ou desativar o roaming?

Na aplicação **Definições,** vá a **Contas**  >  **Sincronizar as suas definições.** A partir desta página, pode ver qual a conta que está a ser usada para percorrer as definições, e pode ativar ou desativar grupos individuais de configurações a serem percorridas.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Qual é a recomendação da Microsoft para permitir o roaming no Windows 10?

A Microsoft tem algumas configurações diferentes disponíveis, incluindo perfis de utilizador roaming, UE-V e Roaming do Estado da Empresa.  A Microsoft está empenhada em fazer um investimento em Enterprise State Roaming em futuras versões do Windows. Se a sua organização não estiver pronta ou confortável com a deslocação de dados para a nuvem, então recomendamos que utilize o UE-V como a sua principal tecnologia de roaming. Se a sua organização necessitar de suporte de roaming para aplicações de ambiente de trabalho do Windows existentes, mas está ansioso para se mover para a nuvem, recomendamos que utilize roaming do Estado da Empresa e UE-V. Embora a UE-V e o Roaming do Estado empresarial sejam tecnologias muito semelhantes, não são mutuamente exclusivas. Complementam-se mutuamente para ajudar a garantir que a sua organização fornece os serviços de roaming de que os seus utilizadores precisam.  

Ao utilizar roaming do Estado da Empresa e UE-V, aplicam-se as seguintes regras:

* Enterprise State Roaming é o principal agente de roaming no dispositivo. A UE-V está a ser usada para complementar a "lacuna Win32".
* O roaming UE-V para as definições do Windows e os dados modernos da aplicação UWP devem ser desativados ao utilizar as políticas do grupo UE-V. Estes já estão cobertos pela Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Como é que o Roaming do Estado da Empresa suporta a infraestrutura virtual de ambiente de trabalho (VDI)?

O Roaming do Estado da Empresa é suportado em SKUs clientes do Windows 10, mas não em SKUs de servidor. Se um VM cliente estiver hospedado numa máquina de hipervisor e iniciar sessão remota na máquina virtual, os seus dados irão circular. Se vários utilizadores partilharem o mesmo SISTEMA e os utilizadores iniciarem o mesmo teste num servidor para uma experiência completa no ambiente de trabalho, o roaming pode não funcionar. Este último cenário baseado em sessão não é oficialmente apoiado.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>O que acontece quando a minha organização compra uma subscrição que inclui a Azure Rights Management depois de usar roaming?

Se a sua organização já estiver a utilizar o roaming no Windows 10 com a subscrição gratuita da Azure Rights Management, a compra de uma [subscrição paga](https://azure.microsoft.com/pricing/details/information-protection/) que inclua o serviço de proteção Azure Rights Management não terá qualquer impacto na funcionalidade do roaming, e não serão necessárias alterações de configuração pelo seu administrador de TI.

## <a name="known-issues"></a>Problemas conhecidos

Consulte a documentação na secção [de resolução de problemas](enterprise-state-roaming-troubleshooting.md) para obter uma lista de questões conhecidas. 

## <a name="next-steps"></a>Passos seguintes 

Para uma visão geral, consulte [a visão geral do roaming do estado da empresa](enterprise-state-roaming-overview.md)