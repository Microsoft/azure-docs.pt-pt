---
title: Enterprise State Roaming FAQ - Diretório Ativo Azure
description: Perguntas frequentes sobre a ESR
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
ms.openlocfilehash: 4ad76835b0c72b691e1ef8810f2c58dedb8f597d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78672376"
---
# <a name="settings-and-data-roaming-faq"></a>FAQ de definições e roaming de dados

Este artigo responde a algumas questões que os administradores de TI podem ter sobre configurações e sincronização de dados de aplicações.

## <a name="what-data-roams"></a>Que dados vagueiam?

**Definições do Windows**: as definições do PC incorporadas no sistema operativo Windows. Geralmente, estas são configurações que personalizam o seu PC, e incluem as seguintes grandes categorias:

* *Tema*, que inclui funcionalidades como o tema do ambiente de trabalho e as definições da barra de tarefas.
* *Configurações*do Internet Explorer, incluindo separadores e favoritos recentemente abertos.
* *Configurações*do navegador Microsoft Edge , como favoritos e lista de leitura.
* *Palavras-passe*, incluindo senhas de Internet, perfis Wi-Fi e outros.
* *Preferências linguísticas,* que incluem configurações para layouts de teclado, linguagem do sistema, data e hora, e muito mais.
* *Facilidade de acesso características*, como tema de alto contraste, Narrador e Magnifier.
* *Outras definições do Windows*, tais como definições do rato.

> [!NOTE]
> Este artigo aplica-se ao navegador baseado no Microsoft Edge Legacy HTML lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Crómio microsoft Edge, lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

**Dados da aplicação**: As aplicações do Universal Windows podem escrever dados de definições para uma pasta de roaming, e quaisquer dados escritos para esta pasta serão automaticamente sincronizados. Cabe ao desenvolvedor de aplicações individual desenhar uma app para tirar partido desta capacidade. Para obter mais informações sobre como desenvolver uma aplicação Universal Windows que utiliza roaming, consulte o blog de armazenamento de dados de [aplicações API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) e o blog de desenvolvimento de [roaming de dados de aplicações do Windows 8.](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx)

## <a name="what-account-is-used-for-settings-sync"></a>Que conta é usada para sincronização de configurações?

No Windows 8.1, as definições sincronizam sempre as contas do consumidor da Microsoft. Os utilizadores da Empresa tinham a capacidade de ligar uma conta da Microsoft à sua conta de domínio Ative Directory para aceder em sincronização de definições. No Windows 10, esta funcionalidade de conta microsoft conectada está a ser substituída por uma estrutura de conta primária/secundária.

A conta principal é definida como a conta usada para iniciar sessão no Windows. Esta pode ser uma conta Microsoft, uma conta Azure Ative Directory (Azure AD), uma conta de Diretório Ativo no local ou uma conta local. Além da conta principal, os utilizadores do Windows 10 podem adicionar uma ou mais contas secundárias na nuvem ao seu dispositivo. Uma conta secundária é geralmente uma conta Microsoft, uma conta AD Azure, ou outra conta, como o Gmail ou o Facebook. Estas contas secundárias fornecem acesso a serviços adicionais, como o único sinal e a Windows Store, mas não são capazes de alimentar a sincronização de configurações.

No Windows 10, apenas a conta principal do dispositivo pode ser utilizada para sincronização de definições (ver [como faço upgrade das definições da conta da Microsoft sincronizadas no Windows 8 para O](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)DA Do Windows 10?

Os dados nunca são misturados entre as diferentes contas de utilizador do dispositivo. Existem duas regras para a sincronização de configurações:

* As definições do Windows irão sempre circular com a conta principal.
* Os dados da aplicação serão marcados com a conta utilizada para adquirir a app. Apenas as aplicações marcadas com a conta primária sincronizarão. A marcação de propriedade da aplicação é determinada quando uma aplicação é carregada lateralmente através da Windows Store ou da gestão de dispositivos móveis (MDM).

Se o proprietário de uma aplicação não puder ser identificado, irá vaguear pela conta principal. Se um dispositivo for atualizado do Windows 8 ou Windows 8.1 para o Windows 10, todas as aplicações serão marcadas como adquiridas pela conta da Microsoft. Isto porque a maioria dos utilizadores adquire aplicações através da Windows Store, e não havia suporte para a Windows Store para contas AD Azure antes do Windows 10. Se uma aplicação for instalada através de uma licença offline, a aplicação será marcada utilizando a conta principal do dispositivo.

> [!NOTE]
> Os dispositivos Windows 10 que são propriedade da empresa e estão ligados ao Azure AD já não podem ligar as suas contas da Microsoft a uma conta de domínio. A capacidade de ligar uma conta microsoft a uma conta de domínio e ter todos os dados sincronizados do utilizador com a conta Microsoft (isto é, a conta Microsoft que circula através da conta microsoft conectada e da funcionalidade Ative Diretor) é removida dos dispositivos do Windows 10 que estão ligados a um ambiente Ative Directory ou Azure AD conectado.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Como faço upgrade das definições da conta da Microsoft sincronizadas no Windows 8 para o Azure AD sincronizar no Windows 10?

Se estiver ligado ao domínio Ative Directory executando o Windows 8.1 com uma conta Microsoft conectada, irá sincronizar as definições através da sua conta Microsoft. Após a atualização para o Windows 10, continuará a sincronizar as definições de utilizador através da conta microsoft, desde que seja um utilizador de domínio e o domínio Ative Directory não se conectem com a AD Azure.

Se o domínio ative diretório no local se ligar ao Azure AD, o seu dispositivo tentará sincronizar as definições utilizando a conta Azure AD conectada. Se o administrador da AD Azure não ativar o Roaming do Estado Da Empresa, a sua conta Azure AD conectada deixará de sincronizar as definições. Se for um utilizador do Windows 10 e iniciar sessão com uma identidade Azure AD, começará a sincronizar as definições do Windows assim que o seu administrador permitir a sincronização de configurações através do Azure AD.

Se armazenou quaisquer dados pessoais no seu dispositivo corporativo, deve estar ciente de que os dados do Windows OS e da aplicação começarão a sincronizar-se com a AD Azure. Isto tem as seguintes implicações:

* As definições pessoais da sua conta Microsoft afastar-se-ão das definições nas suas contas AD Azure de trabalho ou escolar. Isto porque a conta microsoft e a sincronização de definições de Anúncio sinuoso estão agora a utilizar contas separadas.
* Os dados pessoais, tais como senhas Wi-Fi, credenciais web e favoritos do Internet Explorer que foram previamente sincronizados através de uma conta microsoft conectada serão sincronizados via Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Como funciona a conta da Microsoft e a interoperabilidade do Azure AD Enterprise Roaming?

Em novembro de 2015 ou posteriores lançamentos do Windows 10, enterprise state roaming é suportado apenas para uma única conta de cada vez. Se iniciar sessão no Windows utilizando uma conta AD Azure de trabalho ou escolar, todos os dados irão sincronizar via Azure AD. Se iniciar sessão no Windows utilizando uma conta pessoal da Microsoft, todos os dados irão sincronizar através da conta da Microsoft. Os dados universais de aplicação irão circular usando apenas a conta de acesso principal no dispositivo, e só irá circular se a licença da aplicação for propriedade da conta principal. Os dados universais para as aplicações detidas por quaisquer contas secundárias não serão sincronizados.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>As configurações sincronizam contas de Anúncios Azure de vários inquilinos?

Quando várias contas azure ad de diferentes inquilinos da AD Azure estiverem no mesmo dispositivo, você deve atualizar o registo do dispositivo para comunicar com o serviço de Gestão de Direitos Azure para cada inquilino DaD Azure.  

1. Encontre o GUID para cada inquilino da AD Azure. Abra o portal Azure e selecione um inquilino Azure AD. O GUID para o inquilino está na páginahttps://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)Propriedades para o inquilino selecionado ( , identificado **Id do Diretório**. 
2. Depois de ter o GUID, terá de adicionar a chave de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<TENANT ID GUID>**.
   A partir da chave ID GUID do **inquilino,** crie um novo valor multi-string (REG-MULTI-SZ) denominado **AllowedRMSServerUrls**. Para os seus dados, especifique os URLs do ponto de distribuição de licenciamento dos outros inquilinos do Azure a que o dispositivo acede.
3. Pode encontrar os URLs do ponto de distribuição de licenciamento executando o cmdlet **Get-AadrmConfiguration** do módulo AADRM. Se os valores para o **LicenciamentoIntranetDistributionPointUrl** e **LicenciamentoExtranetDistributionPointUrl** forem diferentes, especifique ambos os valores. Se os valores forem os mesmos, especifique o valor apenas uma vez.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Quais são as opções de definições de roaming para aplicações existentes no ambiente de trabalho do Windows?

Roaming só funciona para aplicações Universal Windows. Existem duas opções disponíveis para permitir o roaming numa aplicação de ambiente de trabalho do Windows existente:

* A [Ponte desktop](https://aka.ms/desktopbridge) ajuda-o a levar as suas aplicações de desktop Windows existentes para a Plataforma Universal do Windows. A partir daqui, serão necessárias alterações mínimas de código para tirar partido do roaming de dados da aplicação Azure AD. O Desktop Bridge fornece às suas aplicações uma identidade de aplicação, que é necessária para permitir o roaming de dados de aplicações para aplicações de desktop existentes.
* [A Virtualização da Experiência do Utilizador (UE-V) ajuda-o](https://technet.microsoft.com/library/dn458947.aspx) a criar um modelo de definições personalizadas para aplicações de desktop do Windows existentes e ativar o roaming para aplicações Win32. Esta opção não requer que o desenvolvedor da aplicação mude o código da aplicação. O UE-V limita-se a roaming ative diretório no local para clientes que tenham adquirido o Microsoft Desktop Optimization Pack.

Os administradores podem configurar o UE-V para percorrer os dados das aplicações do Windows, alterando o roaming das definições do Windows OS e dos dados de aplicações universais através das políticas do [grupo UE-V](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), incluindo:

* Política de grupo de configurações do Roam Windows
* Não sincronizar a política do grupo De Aplicações windows
* Internet Explorer roaming na secção de aplicações

No futuro, a Microsoft poderá investigar formas de tornar o UE-V profundamente integrado no Windows e estender o UE-V para percorrer as definições através da nuvem de Anúncio Saque.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Posso armazenar configurações sincronizadas e dados no local?

A Enterprise State Roaming armazena todos os dados sincronizados na nuvem da Microsoft. A UE-V oferece uma solução de roaming no local.

## <a name="who-owns-the-data-thats-being-roamed"></a>Quem é o dono dos dados que estão a ser vagueados?

As empresas possuem os dados que circulavam através do Enterprise State Roaming. Os dados são armazenados num centro de dados Azure. Todos os dados dos utilizadores são encriptados tanto em trânsito como em repouso na nuvem utilizando o serviço de Gestão de Direitos Azure da Azure Information Protection. Esta é uma melhoria em comparação com a sincronização de definições baseadana na conta da Microsoft, que encripta apenas certos dados sensíveis, como credenciais de utilizador antes de deixar o dispositivo.

A Microsoft está empenhada em salvaguardar os dados dos clientes. Os dados de definições de um utilizador da empresa são automaticamente encriptados pelo serviço de Gestão de Direitos do Azure antes de deixar um dispositivo Windows 10, pelo que nenhum outro utilizador pode ler estes dados. Se a sua organização tiver uma subscrição paga para o serviço de Gestão de Direitos do Azure, pode utilizar outras funcionalidades de proteção, tais como rastrear e revogar documentos, proteger automaticamente e-mails que contenham informações sensíveis e gerir as suas próprias chaves (a solução "traga a sua própria chave", também conhecida como BYOK). Para obter mais informações sobre estas funcionalidades e como funciona este serviço de proteção, consulte o que é a [Azure Rights Management.](/azure/information-protection/what-is-information-protection)

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Posso gerir a sincronização para uma aplicação ou configuração específica?

No Windows 10, não existe nenhuma definição de MDM ou Política de Grupo para desativar o roaming para uma aplicação individual. Os administradores de inquilinos podem desativar o sincronização de dados de aplicações para todas as aplicações num dispositivo gerido, mas não existe um controlo mais fino a nível por app ou dentro da app.

## <a name="how-can-i-enable-or-disable-roaming"></a>Como posso ativar ou desativar o roaming?

Na aplicação **Definições,** vá a **Contas** > **Sincronizar as suas definições**. A partir desta página, pode ver qual a conta que está a ser utilizada para percorrer definições, podendo ativar ou desativar grupos individuais de configurações a circular.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Qual é a recomendação da Microsoft para permitir o roaming no Windows 10?

A Microsoft tem algumas soluções de roaming de definições diferentes disponíveis, incluindo Perfis de Utilizador de Roaming, UE-V e Roaming do Estado Empresarial.  A Microsoft está empenhada em fazer um investimento em Roaming do Estado da Empresa em futuras versões do Windows. Se a sua organização não estiver pronta ou confortável com a mudança de dados para a nuvem, então recomendamos que utilize o UE-V como a sua principal tecnologia de roaming. Se a sua organização necessitar de suporte de roaming para aplicações existentes no ambiente de trabalho do Windows, mas está ansioso por se mover para a nuvem, recomendamos que utilize tanto o Enterprise State Roaming como o UE-V. Embora o UE-V e o Enterprise State Roaming sejam tecnologias muito semelhantes, não são mutuamente exclusivas. Complementam-se mutuamente para ajudar a garantir que a sua organização fornece os serviços de roaming de que os seus utilizadores necessitam.  

Ao utilizar tanto o Roaming do Estado Empresarial como o UE-V, aplicam-se as seguintes regras:

* Enterprise State Roaming é o principal agente de roaming no dispositivo. O UE-V está a ser usado para complementar a "lacuna Win32".
* O roaming UE-V para configurações do Windows e dados modernos de aplicações UWP devem ser desativados quando utilizar as políticas do grupo UE-V. Estes já estão cobertos pela Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Como é que o Enterprise State Roaming suporta a infraestrutura virtual de ambiente de trabalho (VDI)?

O Enterprise State Roaming é suportado no Windows 10 client sKUs, mas não no servidor SKUs. Se um VM cliente estiver hospedado numa máquina hipervisor a uma máquina de hipervisor e você iniciar sessão remotamente na máquina virtual, os seus dados irão circular. Se vários utilizadores partilharem o mesmo SISTEMA e os utilizadores iniciarem sessão remotamente num servidor para uma experiência completa no ambiente de trabalho, o roaming pode não funcionar. Este último cenário baseado em sessões não é oficialmente apoiado.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>O que acontece quando a minha organização compra uma subscrição que inclui a Azure Rights Management depois de usar roaming?

Se a sua organização já estiver a utilizar roaming no Windows 10 com a subscrição gratuita de utilização limitada da Azure Rights Management, a aquisição de uma [subscrição paga](https://azure.microsoft.com/pricing/details/information-protection/) que inclua o serviço de proteção de direitos do Azure não terá qualquer impacto na funcionalidade de roaming, e nenhuma alteração de configuração será exigida pelo seu administrador de TI.

## <a name="known-issues"></a>Problemas conhecidos

Consulte a documentação na secção de resolução de [problemas](enterprise-state-roaming-troubleshooting.md) para obter uma lista de questões conhecidas. 

## <a name="next-steps"></a>Passos seguintes 

Para uma visão geral, consulte a [visão geral do estado empresarial](enterprise-state-roaming-overview.md)
