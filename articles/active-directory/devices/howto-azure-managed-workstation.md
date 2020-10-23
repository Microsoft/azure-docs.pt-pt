---
title: Implementar estações de trabalho geridas pela Azure - Azure Ative Directory
description: Aprenda a implementar estações de trabalho seguras geridas pelo Azure para reduzir o risco de violação devido a uma configuração errada ou compromisso.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a56cd23494f65b1c74e44868496855c6e4a32bf7
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92365821"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Implementar uma estação de trabalho segura e gerida pelo Azure

Agora que [compreende postos de trabalho seguros,](concept-azure-managed-workstation.md)é hora de começar o processo de implantação. Com esta orientação, você usa perfis definidos para criar uma estação de trabalho mais segura desde o início.

![Implantação de uma estação de trabalho segura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Selecione um perfil antes de implementar a solução. Pode utilizar vários perfis simultaneamente numa implantação e atribuí-los com etiquetas ou grupos.

> [!NOTE]
> Aplique qualquer um dos perfis necessários pelas suas necessidades. Pode passar para outro perfil atribuindo-o no Microsoft Intune.

| Perfil | Baixo | Melhorada | Alto | Especializada | Protegido | Isolado |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utilizador em Azure AD | Yes | Yes | Yes | Yes | Yes | Yes |
| Gerido por intune | Yes | Yes | Yes | Yes | Yes | Yes |
| Dispositivo - Azure AD registrado | Yes |  |  |  |  | |   |
| Dispositivo - Azure AD juntou-se |   | Yes | Yes | Yes | Yes | Yes |
| Linha de base de segurança intune aplicada |   | Yes <br> (Melhorado) | Yes <br> (Alta Segurança) | Yes <br> (NCSC) | Yes <br> (Seguro) | ND |
| Hardware cumpre padrões seguros do Windows 10 |   | Yes | Yes | Yes | Yes | Yes |
| Microsoft Defender ATP ativado |   | Yes  | Yes | Yes | Yes | Yes |
| Remoção dos direitos de administração |   |   | Yes  | Yes | Yes | Yes |
| Implementação utilizando o Microsoft Autopilot |   |   | Yes  | Yes | Yes | Yes |
| Aplicativos instalados apenas pela Intune |   |   |   | Yes | Yes |Yes |
| URLs restritos à lista aprovada |   |   |   | Yes | Yes |Yes |
| Internet bloqueada (entrada/saída) |   |   |   |  |  |Yes |

> [!NOTE]
> Nos dispositivos de orientação de estação de trabalho seguros serão **atribuídos** perfis e políticas. Os utilizadores não terão as políticas aplicadas diretamente, permitindo que a partilha de dispositivos (dispositivos partilhados) esteja em vigor. Se uma estação de trabalho segura não for partilhada na sua implementação, ou forem necessárias políticas individuais de utilizador, a atribuição dos perfis de política do utilizador pode ser atribuída ao utilizador e ao dispositivo. 

## <a name="license-requirements"></a>Requisitos de licença

Os conceitos abrangidos por este guia pressupõem que tem microsoft 365 Enterprise E5 ou um SKU equivalente. Algumas das recomendações deste guia podem ser implementadas com SKUs mais baixos. Para mais informações, consulte [o licenciamento da Microsoft 365 Enterprise.](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)

Para automatizar o provisionamento de licenças, considere [o licenciamento baseado em grupo](../enterprise-users/licensing-groups-assign.md) para os seus utilizadores.

## <a name="azure-active-directory-configuration"></a>Configuração do Diretório Ativo Azure

O Azure Ative Directory (Azure AD) gere utilizadores, grupos e dispositivos para as suas estações de trabalho de administrador. Ativar serviços de identidade e funcionalidades com uma [conta de administrador.](../roles/permissions-reference.md)

Quando cria a conta de administrador de estação de trabalho segura, expõe a conta à sua estação de trabalho atual. Certifique-se de que utiliza um dispositivo seguro conhecido para fazer esta configuração inicial e toda a configuração global. Para reduzir a exposição ao ataque para a primeira experiência, considere seguir as [orientações para prevenir infeções por malware](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Requerem autenticação de vários fatores, pelo menos para os seus administradores. Consulte [O MFA baseado na nuvem](../authentication/howto-mfa-getstarted.md) para obter orientação de implementação.

### <a name="azure-ad-users-and-groups"></a>Utilizadores e grupos do Azure Active Directory

1. A partir do portal Azure, navegue para **Utilizadores do Azure Ative Directory**  >  **Users**  >  **Novo utilizador**.
1. Crie o administrador do seu dispositivo seguindo os passos no tutorial do [utilizador create](/Intune/quickstart-create-user).
1. Introduza:

   * **Nome** - Administrador de estação de trabalho seguro
   * **Nome do utilizador** - `secure-ws-admin@identityitpro.com`
   * **Papel de diretório**  -  **Administrador limitado** e selecione a função **de Administrador Intune.**

1. Selecione **Criar**.

Em seguida, cria-se dois grupos: utilizadores de estações de trabalho e dispositivos de estação de trabalho.

A partir do portal Azure, navegue até a **Azure Ative Directory**  >  **Groups**  >  **Novo grupo**.

1. Para o grupo de utilizadores de estações de trabalho, é melhor configurar [o licenciamento baseado em grupo](../enterprise-users/licensing-groups-assign.md) para automatizar o fornecimento de licenças aos utilizadores.
1. Para o grupo de utilizadores da estação de trabalho, insira:

   * **Tipo de grupo** - Segurança
   * **Nome do grupo** - Utilizadores seguros da estação de trabalho
   * **Tipo de adesão** - Atribuído

1. Adicione o seu utilizador de administrador de estação de trabalho seguro: `secure-ws-admin@identityitpro.com`
1. Pode adicionar quaisquer outros utilizadores que estejam a gerir estações de trabalho seguras.
1. Selecione **Criar**.
1. Para o grupo de dispositivos de estação de trabalho, insira:

   * **Tipo de grupo** - Segurança
   * **Nome do grupo** - Estações de trabalho seguras
   * **Tipo de adesão** - Atribuído

1. Selecione **Criar**.

### <a name="azure-ad-device-configuration"></a>Configuração do dispositivo AD Azure

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Especificar quem pode juntar dispositivos ao Azure AD

Configure a definição dos seus dispositivos no Ative Directory para permitir que o seu grupo de segurança administrativa se junte a dispositivos ao seu domínio. Para configurar esta definição a partir do portal Azure:

1. Aceda a **Azure Active Directory** > **Dispositivos** > **Definições do dispositivo**.
1. Escolha **Selecionados** em **Utilizadores podem juntar dispositivos ao Azure AD**e, em seguida, selecionar o grupo "Utilizadores de estação de trabalho seguros".

#### <a name="removal-of-local-admin-rights"></a>Remoção dos direitos de administração locais

Este método requer que os utilizadores dos postos de trabalho VIP, DevOps e de nível seguro não tenham direitos de administrador nas suas máquinas. Para configurar esta definição a partir do portal Azure:

1. Aceda a **Azure Active Directory** > **Dispositivos** > **Definições do dispositivo**.
1. Selecione **Nenhum** sob **administradores locais adicionais em dispositivos ligados a Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Requerem autenticação multi-factor para aderir a dispositivos

Para reforçar ainda mais o processo de junção de dispositivos à Azure AD:

1. Aceda a **Azure Active Directory** > **Dispositivos** > **Definições do dispositivo**.
1. Selecione **Sim** em **Exigir que o Multi-Factor Auth se junte a dispositivos**.
1. Selecione **Guardar**.

#### <a name="configure-mobile-device-management"></a>Configure a gestão de dispositivos móveis

Do portal Azure:

1. Navegue pela **Azure Ative Directory**  >  **Mobility (MDM e MAM)**  >  **Microsoft Intune**.
1. Altere a definição de **âmbito do utilizador MDM** para **All**.
1. Selecione **Guardar**.

Estes passos permitem-lhe gerir qualquer dispositivo com o Intune. Para obter mais informações, consulte [Intune Quickstart: Configurar a inscrição automática para dispositivos Windows 10](/Intune/quickstart-setup-auto-enrollment). Cria políticas de configuração e conformidade intune num passo futuro.

#### <a name="azure-ad-conditional-access"></a>Acesso Condicional do Azure AD

O Acesso Condicional AZURE AD pode ajudar a restringir tarefas administrativas privilegiadas a dispositivos compatíveis. Os membros predefinidos do grupo **Secure Workstation Users** são obrigados a realizar a autenticação de vários fatores ao iniciar sessão em aplicações na nuvem. Uma boa prática é excluir da apólice as contas de acesso de emergência. Para obter mais informações, consulte [Gerir as contas de acesso de emergência em Azure AD](../roles/security-emergency-access.md).

## <a name="intune-configuration"></a>Configuração intune

### <a name="configure-enrollment-status"></a>Configure o estatuto de inscrição

É importante garantir que a sua estação de trabalho segura é um dispositivo limpo de confiança. Ao adquirir novos dispositivos, pode insistir que estão definidos para o [Windows 10 Pro no modo S](/Windows/deployment/Windows-10-pro-in-s-mode)– o que limita a exposição a vulnerabilidades durante a gestão da cadeia de fornecimento. Depois de receber um dispositivo do seu fornecedor, pode utilizar o Autopilot para o alterar do modo S. A seguinte orientação fornece detalhes sobre a aplicação do processo de transformação.

Para garantir que os dispositivos estão totalmente configurados antes da utilização, o Intune fornece um meio para bloquear a **utilização do dispositivo até que todas as aplicações e perfis sejam instalados**.

Do **portal Azure:**

1. Aceda ao **Dispositivo Microsoft Intune**  >  **a**  >  **inscrever-se na**  >  **página de inscrição na página**  >  **padrão**  >  **Settings**.
1. Definir **Mostrar o progresso da instalação do perfil da aplicação** para **Sim**.
1. Desa estale **a utilização do dispositivo Block até que todas as aplicações e perfis sejam instalados** no **Sim**.

### <a name="create-an-autopilot-deployment-profile"></a>Criar um perfil de implementação do Autopilot

Depois de criar um grupo de dispositivos, deve criar um perfil de implementação para configurar os dispositivos Autopilot.

Intune no portal Azure:

1. Selecione **perfis**de  >  **implementação de inscrição**  >  **de dispositivos**  >  **Windows Criar Perfil**.
1. Introduza:

   * Nome - **Perfil de implantação de estação de trabalho seguro**.
   * Descrição - **Implantação de postos de trabalho seguros.**
   * Defina **Converter todos os dispositivos visados para o Autopilot** como **Sim**. Esta definição garante que todos os dispositivos na lista são registados com o serviço de implementação do Autopilot. O processo de registo demora até 48 horas, pelo que deverá aguardar.

1. Selecione **Seguinte**.

   * Para **o modo de implantação,** escolha a **implantação de auto-implantação (pré-visualização)**. Os dispositivos com este perfil estão associados ao utilizador que inscreve o dispositivo. Precisa de credenciais de utilizador para inscrever o dispositivo. É essencial notar que a implementação de um dispositivo no modo **de auto-implantação permitirá** implantar portáteis num modelo partilhado. Nenhuma atribuição do utilizador acontecerá até que o dispositivo seja atribuído a um utilizador pela primeira vez. Como resultado, quaisquer políticas de utilizador como o BitLocker não serão ativadas até que uma atribuição do utilizador esteja concluída. Para obter mais informações sobre como iniciar sessão num dispositivo seguro, consulte [perfis selecionados](/intune/device-profile-assign).
   * O **AD De Azure como** caixa deve mostrar **Azure AD unidos** e ser acinzentado.
   * Selecione o seu idioma (Região), **padrão**de tipo de conta de utilizador . 

1. Selecione **Seguinte**.

   * Selecione uma etiqueta de âmbito se tiver pré-configurado.

1. Selecione **Seguinte**.
1. Escolha **atribuições**  >  **Atribuir a**  >  **grupos selecionados.** Em **Grupos Selecionados para incluir**, escolha **estações de trabalho seguras**.
1. Selecione **Seguinte**.
1. Selecione **Criar** para criar o perfil. O perfil de implementação do Autopilot está agora disponível para atribuir a dispositivos.

A inscrição do dispositivo no Autopilot proporciona uma experiência diferente do utilizador com base no tipo e função do dispositivo. No nosso exemplo de implementação, ilustramos um modelo em que os dispositivos seguros são implantados a granel e podem ser partilhados, mas quando utilizados pela primeira vez, o dispositivo é atribuído a um utilizador. Para obter mais informações, consulte [a inscrição do dispositivo Intune Autopilot](/intune/device-enrollment).

### <a name="configure-windows-update"></a>Configurar atualização do Windows

Manter o Windows 10 atualizado é uma das coisas mais importantes que pode fazer. Para manter o Windows num estado seguro, implementa um anel de atualização para gerir o ritmo que as atualizações são aplicadas em estações de trabalho. 

Esta orientação recomenda que crie um novo anel de atualização e altere as seguintes definições predefinidos:

No portal do Azure:

1. Vá ao **Microsoft Intune**  >  **Software atualiza os**  >  **anéis de atualização do Windows 10**.
1. Introduza:

   * Nome - **Atualizações de estações de trabalho geridas pelo Azure**
   * Canal de manutenção - **Windows Insider - Fast**
   * Atualização de qualidade diferimento (dias) - **3**
   * Período de diferimento de atualização de recursos (dias) - **3**
   * Comportamento de atualização automática - **Instalar e reiniciar automaticamente sem controlo do utilizador final**
   * Bloquear utilizador de interromper atualizações do Windows - **Bloco**
   * Exigir a aprovação do utilizador para reiniciar fora do horário de trabalho - **Necessário**
   * Permitir que o utilizador reinicie (reinicialização ativada) - **Obrigatório**
   * Utilizadores de transição para reiniciar após um reinício automático (dias) - **3**
   * Snooze contratado lembrete de reinício (dias) - **3**
   * Prazo definido para reinícios pendentes (dias) - **3**

1. Selecione **Criar**.
1. No **separador Atribuições,** adicione o grupo **Secure Workstations.**

Para obter mais informações sobre as políticas de Atualização do Windows, consulte [A política CSP - Atualização](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integração ATP Intune do Windows Defender

O Windows Defender ATP e o Microsoft Intune trabalham em conjunto para ajudar a prevenir falhas de segurança. Também podem limitar o impacto das infrações. As capacidades ATP fornecem deteção de ameaças em tempo real, bem como permitem uma auditoria extensiva e registo dos dispositivos de ponta.

Para configurar a integração do Windows Defender ATP e Intune, vá ao portal Azure.

1. Navegue para o **Microsoft Intune**  >  **Device Compliance**Windows Defender  >  **ATP**.
1. No passo 1 sob **configuração do Windows Defender ATP**, selecione Connect Windows Defender ATP para o Microsoft **Intune no Windows Defender Security Center**.
1. No Centro de Segurança do Windows Defender:

   1. Selecione **Definições**  >  **Funcionalidades avançadas**.
   1. Para **a ligação Microsoft Intune**, escolha **On**.
   1. Selecione **Guardar preferências**.

1. Depois de estabelecida uma ligação, volte ao Intune e selecione **Refresh** no topo.
1. Defina a opção **Ligar dispositivos Windows com versão 10.0.15063 e posterior a Windows Defender ATP** para **Ativado**.
1. Selecione **Guardar**.

Para obter mais informações, consulte [a Proteção avançada de ameaças do Windows Defender](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Termine o endurecimento do perfil da estação de trabalho

Para completar com sucesso o endurecimento da solução, descarregue e execute o script apropriado. Encontre os links de descarregamento para o **nível**de perfil pretendido:

| Perfil | Localização de descarregamento | Nome de arquivo |
| --- | --- | --- |
| Baixa Segurança | N/D | N/D |
| Segurança Reforçada | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Alta Segurança | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Especializada | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Conformidade Especializada* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Protegido | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* Specialized Compliance é um script que aplica a configuração especializada fornecida na Base de Segurança NCSC Windows10.

Após a execução do script com sucesso, pode fazer atualizações para perfis e políticas no Intune. Os scripts para perfis Melhorados e Seguros criam políticas e perfis para si, mas tem de atribuir a política ao seu grupo **de dispositivos Secure Workstations.**

* Aqui é onde pode encontrar os perfis de configuração do dispositivo Intune criados pelos scripts: **Azure portal**  >  **Microsoft Intune**  >  **Device configuration**  >  **Profiles**.
* Aqui é onde você pode encontrar as políticas de conformidade do dispositivo Intune criadas pelos scripts: **Azure portal**  >  **Microsoft Intune**  >  **Device Compliance**  >  **Policies**.

Para rever as alterações feitas pelos scripts, pode exportar os perfis. Desta forma, pode determinar um endurecimento adicional que pode ser necessário conforme descrito na documentação da [SECCON](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Execute o script de exportação de dados Intune `DeviceConfiguration_Export.ps1` do [repositório DeConfiguration De Dispositivo GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) para exportar todos os perfis intune atuais.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurações adicionais e endurecimento a ter em conta

Seguindo as orientações aqui, implantaste uma estação de trabalho segura. No entanto, também deve considerar controlos adicionais. Por exemplo:

* limitar o acesso a navegadores alternativos
* permitir saída HTTP
* bloquear sites selecionados
* definir permissões para executar scripts personalizados do PowerShell

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Definir regras no prestador de serviços de configuração de firewall (CSP)

Pode fazer alterações adicionais na gestão das regras de entrada e saída, conforme necessário para os seus pontos finais permitidos e bloqueados. À medida que continua a endurecer a estação de trabalho segura, pode afrouxar a restrição que nega todo o tráfego de entrada e saída. Você pode adicionar sites de saída permitidos para incluir sites comuns e confiáveis. Para obter mais informações, consulte [o serviço de configuração firewall](/Windows/client-management/mdm/firewall-csp).

A gestão restritiva do tráfego de URL inclui:

* Negar todo o tráfego de entrada - Definir no script de perfil de estação de trabalho seguro.
* Negar todos os serviços de saída, exceto os serviços selecionados da Azure e da Microsoft, incluindo a Azure Cloud Shell e a capacidade de permitir o Reset da Palavra-Passe Azure.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Se procura fornecer mais granularidade às suas regras de bloqueio, pode consultar o Projeto Spamhaus que mantém [a Lista de Blocos de Domínio (DBL)](https://www.spamhaus.org/dbl/): um bom recurso para usar como um conjunto avançado de regras para implementar para sites de bloqueio.

### <a name="manage-local-applications"></a>Gerir aplicações locais

O posto de trabalho seguro move-se para um estado verdadeiramente endurecido quando as aplicações locais são removidas, incluindo aplicações de produtividade. Aqui, adiciona o Chrome como o navegador padrão e utiliza o Intune para limitar a capacidade de modificar o navegador e os seus plug-ins.

#### <a name="deploy-applications-using-intune"></a>Implementar aplicações usando o Intune

Em algumas situações, aplicações como o navegador Google Chrome são necessárias na estação de trabalho segura. O exemplo a seguir fornece instruções para instalar o Chrome em dispositivos no grupo de segurança **Secure Workstations**.

1. Descarregue o pacote chrome instalador offline [para windows 64-bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extraia os ficheiros e tome nota da localização do `GoogleChromeStandaloneEnterprise64.msi` ficheiro.
1. No **portal Azure** navegue para aplicações do **Cliente Microsoft Intune**  >  **Client apps**  >  **Apps**  >  **Add**.
1. Sob **o tipo de App,** escolha Linha de **negócios.**
1. No **ficheiro pacote app**, selecione o ficheiro a partir da `GoogleChromeStandaloneEnterprise64.msi` localização extraída e selecione **OK**.
1. Ao abrigo **das informações da App,** forneça uma descrição e um editor. Selecione **OK**.
1. Selecione **Adicionar**.
1. No **separador Atribuições,** selecione **Disponível para dispositivos matriculados** no **tipo de atribuição**.
1. Em **Grupos Incluídos**, adicione o grupo **Secure Workstations.**
1. Selecione **OK**e, em seguida, selecione **Guardar**.

Para obter mais informações sobre configurar as definições do Chrome, consulte [Gerir o Navegador Chrome com o Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurar o portal da empresa para aplicações personalizadas

Em modo seguro, a instalação da aplicação é restrita ao portal da empresa Intune. No entanto, a instalação do portal requer acesso à Microsoft Store. Na solução segura, pode disponibilizar o portal da empresa a todos os dispositivos através de um modo offline.

Uma cópia gerida pela Intune do Portal da [Empresa](/Intune/store-apps-company-portal-app) dá-lhe acesso a pedido a ferramentas adicionais que pode empurrar para os utilizadores dos postos de trabalho seguros.

Poderá ser necessário instalar aplicações do Windows 32 bits ou outras aplicações cuja implementação exija preparações especiais. Nesses casos, a [ferramenta de preparação de conteúdo win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) da Microsoft pode fornecer um ficheiro de formato pronto a usar `.intunewin` para instalação.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Acesso Condicional apenas permitindo capacidade de estação de trabalho segura para aceder ao portal Azure

O Azure AD oferece a capacidade de gerir e restringir, quem e o que pode aceder ao seu portal de gestão de nuvem Azure. Permitir o [Acesso Condicional](../conditional-access/overview.md) garantirá que apenas a sua estação de trabalho segura pode gerir ou alterar recursos. É essencial que ao implementar esta funcionalidade considere, se a funcionalidade de [acesso de emergência](../roles/security-emergency-access.md) pode ou deve ser usada apenas para casos extremos e a conta gerida através da política.

> [!NOTE]
> Terá de criar um grupo de utilizadores e incluir o utilizador de emergência que pode contornar a política de Acesso Condicional. Por exemplo, temos um grupo de segurança chamado **Emergency BreakGlass**

1. Navegue pelo **portal Azure**  >  **Microsoft Intune**  >  **Conditional Access - Políticas**Novas Políticas  >  **.**
1. Fornecer um **nome** para a apólice.
1. Selecione **Utilizador e Grupos**  >  **Selecione utilizadores e grupos** 
1. **Selecione incluir**  >  **funções de diretório** > Escolha as funções > Administrador Global, Administrador de Função Privilegiada, Administrador de Autenticação Privilegiada, Administrador de Segurança, Administrador de Conformidade, Administrador de Acesso Condicional, Administrador de Aplicação, Administrador de Aplicação em Nuvem, Administrador de Serviço Intune
1. **Selecione excluir** > escolha **utilizadores e grupos** > **Selecione selecionar utilizadores excluídos** > Selecione o seu grupo **de BreakGlass de emergência.**
1. Selecione **aplicativos cloud ou ações** > Selecione **todas as aplicações em nuvem**
1. Selecione **condições** > selecione **plataformas de dispositivos** > escolha configurar **Sim** > **Selecione plataformas de dispositivo selecionar** **o Windows**
1. Selecione **os controlos de acesso** > Selecione Grant **Access** **Yes** > Choose Require o dispositivo para ser marcado **como conforme**. 
1. Selecione **a política de ativação**  >  **em**
 
Este conjunto de políticas garantirá que os seus Administradores devem utilizar um dispositivo Windows em conformidade, que é definido pela Intune e pelo WDATP. 

As organizações também devem considerar o bloqueio de protocolos de autenticação de legados nos seus ambientes. Existem múltiplas formas de realizar esta tarefa, para mais informações sobre o bloqueio de protocolos de autenticação de legados ver o artigo, [Como: Bloquear a autenticação do legado a AD AZure com Acesso Condicional](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Use PowerShell para criar configurações personalizadas

Também pode utilizar o PowerShell para alargar as capacidades de gestão do anfitrião. Este script de exemplo configura um fundo padrão no anfitrião. É uma capacidade que também está disponível através do portal Eszure e perfis. O script de exemplo serve apenas para ilustrar a funcionalidade PowerShell.

Poderá ser necessário configurar alguns controlos e configurações personalizadas nas suas estações de trabalho seguras. Este exemplo altera o fundo da estação de trabalho utilizando a capacidade da Powershell de identificar facilmente o dispositivo como uma estação de trabalho pronta a utilizar e segura.

O [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) script do Microsoft Scripting Center permite ao Windows carregar esta [imagem de fundo genérica gratuita](https://i.imgur.com/OAJ28zO.png) no arranque.

1. Descarregue o script para um dispositivo local.
1. Atualize o clienteXXXX e a localização de descarregamento da imagem de fundo. No nosso exemplo, substituímos o clienteXXXX em fundos.
1. Navegue para o **portal Azure**  >  **Microsoft Intune**Device  >  **configuração**  >  **PowerShell Scripts**  >  **Add**.
1. Forneça um **Nome** para o script e especifique a **localização**do Script .
1. Selecione **Configurar**.
   1. **Descreva este script utilizando as credenciais registadas** para **Sim**.
   1. Selecione **OK**.
1. Selecione **Criar**.
1. Selecione **atribuições**  >  **Selecionar grupos**.
   1. Adicione o grupo de segurança **Secure Workstations**.
   1. Selecione **Guardar**.

## <a name="enroll-and-validate-your-first-device"></a>Inscreva-se e valide o seu primeiro dispositivo

1. Para inscrever o seu dispositivo, necessita das seguintes informações:
   * **Número de série** - encontrado no chassis do dispositivo.
   * **ID do produto do Windows** - encontrado no **sistema**  >  **Sobre** a partir do menu De Definições do Windows.
   * Pode executar [o Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) para obter um ficheiro de hash CSV com todas as informações necessárias para a inscrição do dispositivo.
   
     Corra `Get-WindowsAutoPilotInfo – outputfile device1.csv` para a produção da informação como um ficheiro CSV que pode importar para Intune.

     > [!NOTE]
     > O guião requer direitos elevados. Funciona como um contrato remoto. O `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` comando permite que o script corra corretamente.

   * Pode recolher estas informações insinuá-la num dispositivo windows 10 ou superior. O seu revendedor de hardware também pode fornecer estas informações.
1. No **portal Azure**, aceda aos dispositivos de inscrição do Windows do **Dispositivo Microsoft Intune**  >  **Device enrollment**  >  **Windows enrollment**  >  **- Gerir dispositivos Windows Autopilot**.
1. **Selecione Import** e escolha o seu ficheiro CSV.
1. Adicione o dispositivo ao grupo de segurança **Secure Workstations.**
1. No dispositivo Windows 10 que pretende configurar, aceda à Atualização de **Definições**do Windows  >  **&**  >  **Recuperação de**Segurança .
   1. Escolha **Começar a trabalhar** no Reset this **PC**.
   1. Siga as indicações para reiniciar e reconfigurar o dispositivo com as políticas de perfil e conformidade configuradas.

Depois de configurar o dispositivo, complete uma revisão e verifique a configuração. Confirme se o primeiro dispositivo está configurado corretamente antes de continuar a sua implantação.

## <a name="assign-devices"></a>Atribuir dispositivos

Para atribuir dispositivos e utilizadores, é necessário mapear os [perfis selecionados](/intune/device-profile-assign) para o seu grupo de segurança. Todos os novos utilizadores que necessitem de permissões para o serviço também devem ser adicionados ao grupo de segurança.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Utilização do Sentinel e do Windows Defender ATP para monitorizar e responder a incidentes de segurança

Monitorizar a implementação segura da estação de trabalho pode ser realizado permitindo [Sentinel] e utilizando [a Gestão de Ameaças e Vulnerabilidades](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) A orientação não proporcionará uma caça exaustiva de ameaças, mas fornecerá esforços de bom senso para monitorizar e responder a potenciais incidentes de segurança.

Usaremos **Azure Sentinel** para: 

* Recolher dados do Intune, portal Azure e Azure AD para monitorização de utilizadores e dispositivos
* Ajude a Investigar a configuração de utilizador e dispositivo atividade suspeita
* E impulsionar a capacidade de explorar investigações de segurança usando wDATP

A monitorização do Sentinel requer que sejam configurados conectores para as suas fontes de dados, como o Azure AD.

1. No **portal Azure**, vá a **Azure Sentinel (Preview)** > Select **Add**
1. No **Choose um espaço de trabalho para adicionar ao Azure Sentinel** Select **Crie um novo espaço de trabalho**
1. Introduza:
   * **Log Analytics Workspace** - 'Secure Workstation Monitoring'
   * **Subscrição** - Selecione a sua subscrição ativa
   * **Grupo de Recursos** - selecione o ** Criar novo** > Secure Workstation RG > **Ok**
   * **Localização** - Selecione a localização que é geograficamente mais adequada para a sua implantação
   * **Nível de Preço** - Selecione **Por GB (2018)**
1. Selecione **OK**.

Em seguida, ligaremos fontes de dados de estações de trabalho seguras disponíveis à monitorização.

1. No **portal Azure**, vá ao **espaço de trabalho Azure Sentinel** > Select Secure **Workstation Monitoring** workspace
1. Selecione **conectores de dados**
1. Escolha **O Diretório Ativo Azure** > página de conector aberto > depois de rever o Pré-requisito. Dirija-se à Configuração e selecione **Connect** para os registos de registo azure AD, bem como para os registos de auditoria AD Azure.
1. Escolha **Azure Activity** > Abrir página do conector > depois de rever o Pré-requisito. Proceda a Configure Azure Activity Logs > Selecione a sua subscrição > Select **Connect**

À medida que os dados são recolhidos pelo Sentinel poderá observar a atividade selecionando o **portal Azure,** vá ao **Azure Sentinel Overview** 

Utilizaremos **o Windows Defender ATP (WDATP)** para:

* Observar e monitorizar continuamente vulnerabilidades e configurações erradas
* Utilize o WDATP para priorizar ameaças dinâmicas na natureza
* Acionar a correlação de vulnerabilidades com alertas de deteção e resposta de ponto final (EDR)
* Utilize o painel de instrumentos para identificar a vulnerabilidade ao nível da máquina durante as investigações
* Empurre as remediações para Intune

Configure o [painel ATP do Defender](https://securitycenter.windows.com/machines). Utilizar orientação no [painel de instrumentos de gestão de vulnerabilidades de & de ameaça](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Monitorização da atividade da aplicação utilizando o Agente de Monitorização da Microsoft (MMA)
A partir da estação de trabalho especializada, o cacifo de aplicações está habilitado para monitorização da atividade da aplicação numa estação de trabalho. Para que a monitorização seja integrada no seu espaço de trabalho Log Analytics, deve ser seguido um agente e configuração de MMA. 

> [!NOTE]
> O perfil de estação de trabalho especializado contém as políticas de monitorização do AppLocker. A implementação das políticas é necessária para o acompanhamento da atividade de aplicação num cliente

Implementar o agente MMA com script Intune PowerShell

1. Descarregue o script de configuração [para um dispositivo local](https://aka.ms/securedworkstationgit).
1. Atualizar os parâmetros, **$WorkSpaceID** e **$WorkSpaceKey**
1. Navegue para o **portal Azure**  >  **Microsoft Intune**Device  >  **configuração**  >  **PowerShell Scripts**  >  **Add**.
1. Forneça um **Nome** para o script e especifique a **localização**do Script .
1. Selecione **Configurar**.
   1. **Descreva este script utilizando as credenciais registadas** para **Sim**.
   1. Selecione **OK**.
1. Selecione **Criar**.
1. Selecione **atribuições**  >  **Selecionar grupos**.
   1. Adicione o grupo de segurança **Secure Workstations**.
   1. Selecione **Guardar**.

Em seguida, tem de configurar o Log Analytics para receber os novos registos
1. No **portal Azure**, vá ao **Log Analytics Workspace** > Select - 'Secure Workstation Monitoring'
1. Selecione **registos de eventos**de data windows de definições  >  **Data**  >  **avançadas**
1. Em **Recolher eventos a partir dos seguintes registos** de eventos 
1. Introduza:
   * 'Microsoft-Windows-AppLocker/EXE e DLL' > Unselect **Informational**
   * 'Microsoft-Windows-AppLocker/MSI e Script' > **Deselect**
   * 'Microsoft-Windows-AppLocker/Packaged app-Deployment' > Unselect **Informational**
   * 'Microsoft-Windows-AppLocker/Packaged app-execution' > Unselect **Informational**
1. Selecione **Guardar**

O registo de aplicações estará disponível no espaço de trabalho selecionado do Log Analytics.

## <a name="monitoring"></a>Monitorização

* Saiba como [detetar ameaças com Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md)
* [Investigue incidentes com Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
* [Configurar respostas automáticas de ameaças em Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
* Entenda como rever a sua [Pontuação de Exposição](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Recomendação [de segurança de revisão](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Gerir [remediações de](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) segurança
* Gerir a [deteção e resposta do ponto final](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Monitorize perfis com [monitorização do perfil Intune.](/intune/device-profile-monitor)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Microsoft Intune.](/intune/index)
* Compreenda [o anúncio do Azure.](../index.yml)
* Trabalhar com [a Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Descubra [Azure Sentinel](../../sentinel/index.yml)