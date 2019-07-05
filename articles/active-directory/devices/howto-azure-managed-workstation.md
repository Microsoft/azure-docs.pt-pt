---
title: Implantar estações de trabalho geridos pelo Azure - Azure Active Directory
description: Saiba como implementar estações de trabalho seguras e gerida pelo Azure para reduzir o risco de violação devido a configuração incorreta ou o comprometimento.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550490"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Implementar uma estação de trabalho segura e gerida pelo Azure

Agora que [compreender as estações de trabalho seguras](concept-azure-managed-workstation.md), chegou a hora para iniciar o processo de implantação. Com esta orientação, utilize perfis definidos para criar uma estação de trabalho que é mais segura desde o início.

![Implementação de uma estação de trabalho segura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Tem de selecionar um perfil antes de poder implementar a solução. Pode utilizar vários perfis em simultâneo numa implementação e atribuí-las com etiquetas ou grupos.
> [!NOTE]
> Aplicam-se qualquer um dos perfis conforme necessário, ao seus requisitos. Pode mover a outro perfil ao atribuí-lo no Intune.

| Perfil | Baixa | Melhorada | Alta | Especializada | Protegido | Isolado |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utilizador no Azure AD | Sim | Sim | Sim | Sim | Sim | Sim |
| Intune-managed | Sim | Sim | Sim | Sim | Sim | Sim |
| Dispositivo - Azure AD registado | Sim |  |  |  |  | |   |
| Dispositivo - associados ao Azure AD |   | Sim | Sim | Sim | Sim | Sim |
| Base de segurança do Intune aplicada |   | Sim <br> (Avançado) | Sim <br> (HighSecurity) | Sim <br> (NCSC) | Sim <br> (Protegido) |  ND |
| Hardware de cumprir os padrões de 10 seguro do Windows |   | Sim | Sim | Sim | Sim | Sim |
| Microsoft Defender ATP ativada |   | Sim  | Sim | Sim | Sim | Sim |
| Remoção de direitos de administrador |   |   | Sim  | Sim | Sim | Sim |
| Implementação com o Microsoft Autopilot |   |   | Sim  | Sim | Sim | Sim |
| Aplicações instaladas apenas pelo Intune |   |   |   | Sim | Sim |Sim |
| URLs restringidas a lista aprovada |   |   |   | Sim | Sim |Sim |
| Internet bloqueado (entrada/saída) |   |   |   |  |  |Sim |

## <a name="license-requirements"></a>Requisitos de licença

Os conceitos abordados neste guia partem do princípio de que tem o Microsoft 365 Enterprise E5 ou um SKU equivalente. Algumas das recomendações neste guia podem ser implementadas com SKUs inferior. Para obter mais informações, consulte [licenciamento do Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Para automatizar o provisionamento de licença, considere [licenciamento baseado em grupo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) para os seus utilizadores.

## <a name="azure-active-directory-configuration"></a>Configuração do Active Directory do Azure

Azure Active Directory (Azure AD) gere os utilizadores, grupos e dispositivos para suas estações de trabalho do administrador. Tem de ativar os serviços de identidade e de recursos com um [conta de administrador](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Quando cria a conta de administrador de estação de trabalho protegida, exposto a conta para a estação de trabalho atual. Certifique-se de que utilizar um dispositivo de segurança conhecido para fazer esta configuração inicial e toda a configuração global. Para reduzir a exposição de ataque para a experiência de iniciantes, considere seguir a [documentação de orientação para impedir infeções de software maligno](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

Deve também exigir autenticação multifator, pelo menos para os administradores. Ver [implementar o MFA baseado na nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) para obter orientações de implementação.

### <a name="azure-ad-users-and-groups"></a>Grupos e utilizadores do Azure AD

1. No portal do Azure, navegue até **do Azure Active Directory** > **utilizadores** > **novo utilizador**.
1. Criar o seu administrador de dispositivo ao seguir os passos a [tutorial de utilizador de criar](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Introduza:
   * **Nome** -proteger o administrador de estação de trabalho
   * **Nome de utilizador** - `secure-ws-admin@identityitpro.com`
   * **Função de diretório** - **administrador limitado** e selecione o **administrador do Intune** função.
1. Selecione **Criar**.

Em seguida, cria dois grupos: os utilizadores da estação de trabalho e dispositivos de estação de trabalho.

No portal do Azure, navegue até **do Azure Active Directory** > **grupos** > **novo grupo**.

1. Para o grupo de utilizadores da estação de trabalho, pode querer configurar [licenciamento baseado em grupo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) para automatizar o aprovisionamento de licenças aos utilizadores.
1. O grupo de utilizadores da estação de trabalho, introduza:
   * **Tipo de grupo** -segurança
   * **Nome do grupo** -os utilizadores de estação de trabalho seguro
   * **Tipo de associação** - atribuído
1. Adicione o seu utilizador de administrador de estação de trabalho segura: `secure-ws-admin@identityitpro.com`
1. Pode adicionar outros utilizadores que pretende gerir estações de trabalho seguras.
1. Selecione **Criar**.

1. Para o grupo de dispositivos de estação de trabalho, introduza:
   * **Tipo de grupo** -segurança
   * **Nome do grupo** -proteger estações de trabalho
   * **Tipo de associação** - atribuído
1. Selecione **Criar**.

### <a name="azure-ad-device-configuration"></a>Configuração de dispositivo do Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Especifique quem pode associar dispositivos ao Azure AD

Configure os seus dispositivos definição no Active Directory para permitir que o seu grupo de segurança administrativa para associar dispositivos ao seu domínio. Para configurar esta definição no portal do Azure:

1. Aceda a **do Azure Active Directory** > **dispositivos** > **definições do dispositivo**.
1. Escolher **Selected** sob **os utilizadores podem associar dispositivos ao Azure AD**e, em seguida, selecione o grupo "Utilizadores da estação de trabalho seguro".

#### <a name="removal-of-local-admin-rights"></a>Remoção de direitos de administrador local

Este método requer que os utilizadores do VIP, DevOps e ao nível do proteger estações de trabalho ter sem direitos de administrador nos seus computadores. Para configurar esta definição no portal do Azure:

1. Aceda a **do Azure Active Directory** > **dispositivos** > **definições do dispositivo**.
1. Selecione **None** sob **dispositivos associados de administradores locais adicionais no Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Exigir autenticação multifator para associar dispositivos

Para reforçar ainda mais o processo de associação de dispositivos para o Azure AD:

1. Aceda a **do Azure Active Directory** > **dispositivos** > **definições do dispositivo**.
1. Selecione **Sim** sob **requerer o multi-Factor Auth para associar dispositivos**.
1. Selecione **Guardar**.

#### <a name="configure-mdm"></a>Configurar a MDM

No portal do Azure:

1. Navegue até **do Azure Active Directory** > **mobilidade (MDM e MAM)**  > **Microsoft Intune**.
1. Alteração da **âmbito do utilizador MDM** na definição **todos os**.
1. Selecione **Guardar**.

Estes passos permitem-lhe gerir qualquer dispositivo com o Intune. Para obter mais informações, consulte [guia de introdução do Intune: Configurar a inscrição automática para dispositivos Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Criar políticas de configuração e de conformidade do Intune num passo posterior.

#### <a name="azure-ad-conditional-access"></a>Acesso Condicional no Azure AD

De acesso condicional do AD do Azure pode ajudar a restringir as tarefas administrativas com privilégios para dispositivos em conformidade. Predefinidas membros do **proteger os utilizadores da estação de trabalho** grupo são necessários para efetuar a autenticação multifator ao iniciar sessão em aplicações na cloud. Uma prática recomendada é excluir contas de acesso de emergência da política. Para obter mais informações, consulte [gerir contas de acesso de emergência no Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Para configurar o acesso condicional do portal do Azure:

1. Aceda a **do Azure Active Directory** > **acesso condicional** > **nova política**.
1. Introduza:
   * **Nome** -seguro de dispositivos necessários política
   * Atribuições
     * **Utilizadores e grupos**
       * Incluir - **utilizadores e grupos** – selecione o **proteger usuários de estação de trabalho** grupo criado anteriormente.
       * Excluir - **utilizadores e grupos** -selecione contas de acesso de emergência da sua organização.
     * **Aplicações na cloud** -incluem **todas as aplicações na cloud**.
    * Controlos de acesso
      * **Concessão** - selecione **conceder acesso** botão de opção.
        * **Exigir autenticação multifator**.
        * **Exigir dispositivo seja marcado como compatível**.
        * Para vários controles - **necessitam de todos os controlos selecionados**.
    * Ativar política - **no**.

Tem a opção para criar políticas que bloquear países em que os utilizadores não seriam aceder a recursos da empresa. Para obter mais informações sobre políticas de acesso condicional com base na localização de IP, consulte [a condição de localização no Azure Active Directory condicional acesso](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Configuração do Intune

### <a name="configure-enrollment-status"></a>Configurar o estado de inscrição

É importante certificar-se de que a estação de trabalho segura é um dispositivo fidedigno limpo. Quando compra de novos dispositivos, pode insistir que eles factory definido como [Windows 10 Pro no modo de S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), que limita a exposição a vulnerabilidades durante a gestão de cadeias de fornecimento. Depois de receber um dispositivo de seu fornecedor, pode utilizar o Autopilot para alterá-lo do modo de S. As seguintes orientações fornecem detalhes sobre o processo de transformação a aplicar.

Para garantir que os dispositivos são totalmente configurados antes do uso, o Intune fornece um meio **bloqueie a utilização de dispositivos, até que todas as aplicações e perfis são instalados**.

Partir do **portal do Azure**:
1. Aceda a **Microsoft Intune** > **inscrição de dispositivos** > **inscrição Windows** > **estado de inscrição Página** > **predefinido** > **definições**.
1. Definir **Mostrar o progresso de instalação do perfil de aplicação** ao **Sim**.
1. Definir **bloqueie a utilização de dispositivos, até que todas as aplicações e perfis são instalados** ao **Sim**.

### <a name="create-an-autopilot-deployment-profile"></a>Criar um perfil de implementação do Autopilot

Depois de criar um grupo de dispositivos, tem de criar um perfil de implementação para configurar os dispositivos Autopilot.

No Intune no portal do Azure:

1. Selecione **inscrição de dispositivos** > **inscrição Windows** > **perfis de implementação** > **criar perfil** .
1. Introduza:
   * Nome - **proteger o perfil de implementação de estação de trabalho**.
   * Descrição - **implementação de estações de trabalho seguras**.
   * Definir **converter destino todos os dispositivos Autopilot** ao **Sim**. Esta definição certifica-se de que todos os dispositivos na lista registe-se com o serviço de implementação do Autopilot. O processo de registo demora até 48 horas, pelo que deverá aguardar.
1. Selecione **Seguinte**.
   * Para **modo de implementação**, escolha **Self-implantação (pré-visualização)** . Dispositivos com este perfil estão associados com o utilizador que inscreve o dispositivo. Precisa de credenciais de utilizador para inscrever o dispositivo.
   * O **aderir ao Azure AD como** deve mostrar caixa **associados ao Azure AD** e fique esmaecido.
   * Selecione seu Langugage (região), o tipo de conta de utilizador **padrão**. 
1. Selecione **Seguinte**.
   * Selecione uma etiqueta de âmbito, se o ter pré-configurada um.
1. Selecione **Seguinte**.
1. Escolher **atribuições** > **atribuir ao** > **grupos selecionados**. Na **selecionar grupos para incluir**, escolha **proteger usuários de estação de trabalho**.
1. Selecione **Seguinte**.
1. Selecione **Criar** para criar o perfil. O perfil de implementação do Autopilot está agora disponível para atribuir a dispositivos.

### <a name="configure-windows-update"></a>Configurar o Windows Update

Manter o Windows 10 estão atualizados é uma das coisas mais importantes que pode fazer. Para manter o Windows num estado seguro, implemente uma cadência de atualização para gerir o ritmo que as atualizações são aplicadas para estações de trabalho. 

Esta orientação recomenda que criar um novo anel de atualização e alterar as seguintes predefinições:

No portal do Azure:

1. Aceda a **Microsoft Intune** > **atualizações de Software** > **Cadências de atualização do Windows 10**.
1. Introduza:
   * Nome - **atualizações de estação de trabalho de geridas do Azure**
   * Canal - de serviço **Windows Insider - rápido**
   * Diferimento de atualizações de qualidade (dias) - **3**
   * Período de diferimento da atualização de funcionalidades (dias) - **3**
   * Comportamento de atualização automática - **instalação automática e reinicio sem controle de usuário final**
   * Impedir o utilizador da colocar em pausa atualizações do Windows - **bloco**
   * Exigir a aprovação do utilizador reinicie fora das horas de trabalho – **necessário**
   * Permitir ao utilizador para reiniciar (reinício envolvido) - **necessário**
   * A transição de que os usuários envolvido reinício após um reinício automático (dias) - **3**
   * Lembrete de reinício envolvidos suspender (dias) - **3**
   * Definir o prazo para pendente é reiniciado (dias) - **3**

1. Selecione **Criar**.
1. Sobre o **atribuições** separador, adicione o **proteger estações de trabalho** grupo.

Para obter informações adicionais sobre as políticas de atualização do Windows, consulte [CSP de política - atualização](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integração do Intune do Windows Defender ATP

Windows Defender ATP e o Microsoft Intune funcionam em conjunto para ajudar a evitar falhas de segurança. Eles também podem limitar o impacto de falhas. Capacidades do ATP fornecem a deteção de ameaças em tempo real, bem como ativar extensivo de auditoria e registo de dispositivos de ponto de extremidade.

Para configurar a integração do Windows Defender ATP e do Intune, aceda ao portal do Azure.

1. Navegue até **Microsoft Intune** > **conformidade do dispositivo** > **do Windows Defender ATP**.
1. No passo 1 sob **configurar o Windows Defender ATP**, selecione **ligar o Windows Defender ATP ao Microsoft Intune no Centro de segurança do Windows Defender**.
1. No Centro de segurança do Windows Defender:
   1. Selecione **configurações** > **funcionalidades avançadas**.
   1. Para **ligação do Microsoft Intune**, escolha **no**.
   1. Selecione **salvar preferências**.
1. Depois de uma ligação é estabelecida, volte ao Intune e selecione **atualizar** na parte superior.
1. Definir **ligar o Windows versão de dispositivos 10.0.15063 e superior para o Windows Defender ATP** para **no**.
1. Selecione **Guardar**.

Para obter mais informações, consulte [a proteção de ameaças avançada do Windows Defender](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Terminar perfil de estação de trabalho sistema de proteção

Para concluir com êxito o sistema de proteção da solução, transferir e executar o script adequado. Encontrar os links de download para seu pretendido **nível de perfil**:

| Perfil | Localização de transferência | Nome de ficheiro |
| --- | --- | --- |
| Segurança baixa | N/A |  N/A |
| Segurança Melhorada | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Alta segurança  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Especializada | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Conformidade especializadas * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Protegido | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* Conformidade especializada é um script que impõe a configuração especializada fornecida no SecurityBaseline de Windows 10 NCSC.

Depois do script for executado com êxito, pode efetuar atualizações às políticas no Intune e perfis. Os scripts para perfis avançado e seguro criam políticas e perfis para, mas tem de atribuir a política para o seu **proteger estações de trabalho** grupo.

* É aqui onde é possível encontrar os perfis de configuração de dispositivos do Intune criados pelos scripts: **Portal do Azure** > **Microsoft Intune** > **configuração do dispositivo** > **perfis**.
* É aqui onde é possível encontrar o dispositivo do Intune criadas pelos scripts de políticas de conformidade: **Portal do Azure** > **Microsoft Intune** > **conformidade de dispositivos** > **políticas**.

Para rever as alterações efetuadas pelos scripts, pode exportar os perfis. Dessa forma pode determinar proteção adicional que possam ser necessária, conforme descrito na documentação do SECCON.

Execute o script de exportação de dados do Intune `DeviceConfiguration_Export.ps1` partir do [DeviceConfiguration GiuHub repositório](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) para exportar todos os perfis do Intune atuais.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurações adicionais e proteção a considerar

Ao seguir as diretrizes aqui, implementou uma estação de trabalho segura. No entanto, também deve considerar controlos adicionais. Por exemplo:

* limitar o acesso a navegadores alternativos
* permitir HTTP de saída
* bloquear sites selecionados
* definir permissões para a execução de scripts personalizados do PowerShell

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Definir regras no fornecedor de serviço de configuração da firewall (CSP)

Pode fazer alterações adicionais para a gestão de regras de entrada e saídas, conforme necessário para os pontos finais do permitidos e bloqueados. À medida que continua a proteger a estação de trabalho segura, pode flexibilize as a restrição que nega todo o tráfego de entrada e saído. Pode adicionar sites de saída permitidos para incluir os Web sites fidedignos e comuns. Para obter mais informações, consulte [serviço de configuração de Firewall](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Recomendações de padrão restringida são:

* Negar toda a entrada
* Negar toda a saída

O projeto de Spamhaus mantém [a lista de blocos de domínio (DBL)](https://www.spamhaus.org/dbl/): um bom recurso para utilizar como um ponto de partida para bloquear sites.

### <a name="manage-local-applications"></a>Gerir aplicações locais

A estação de trabalho segura passa ao Estado verdadeiramente protegido quando aplicativos locais são removidos, incluindo aplicativos de produtividade. Aqui, adicionar Chrome como browser predefinido e utilizar o Intune para limitar a capacidade de um utilizador de modificar o navegador e seu plug-ins.

#### <a name="deploy-applications-using-intune"></a>Implementar aplicações com o Intune

Em algumas situações, os aplicativos como o browser Google Chrome são necessários na estação de trabalho protegida. O exemplo a seguir fornece instruções para instalar o Chrome para dispositivos no grupo de segurança **proteger estações de trabalho**.

1. Transferir o instalador offline [pacote de Chrome para Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extraia os ficheiros e tome nota da localização do `GoogleChromeStandaloneEnterprise64.msi` ficheiro.
1. Na **portal do Azure** procure **Microsoft Intune** > **aplicações de cliente** > **aplicações**  >  **Adicionar**.
1. Sob **tipo de aplicação**, escolha **linha de negócio**.
1. Sob **o ficheiro de pacote de aplicação**, selecione a `GoogleChromeStandaloneEnterprise64.msi` do ficheiro da localização extraída e selecione **OK**.
1. Sob **informações da aplicação**, indique uma descrição e um publicador. Selecione **OK**.
1. Selecione **Adicionar**.
1. Sobre o **atribuições** separador, selecione **disponível para dispositivos inscritos** sob **tipo de atribuição**.
1. Sob **grupos incluídos**, adicione o **proteger estações de trabalho** grupo.
1. Selecione **OK**e, em seguida, selecione **guardar**.

Para obter mais orientações sobre como configurar as definições do Chrome, consulte [gerir Browser do Chrome com o Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurar o portal da empresa para aplicações personalizadas

Num modo seguro, a instalação do aplicativo está limitada ao portal da empresa do Intune. No entanto, se instalar o portal requer acesso ao Microsoft Store. Na sua solução segura, pode fazer o portal da empresa disponível para todos os dispositivos por meio de um modo offline.

Uma cópia geridos pelo Intune a [Portal da empresa](https://docs.microsoft.com/Intune/store-apps-company-portal-app) dá-lhe acesso a pedido para ferramentas adicionais que possa Empurrar para baixo para os usuários de estações de trabalho protegidas.

Poderá ter de instalar aplicações do Windows de 32 bits ou de outras aplicações cuja implementação requerem preparações especiais. Nesses casos, o [ferramenta de preparação do Microsoft win32 conteúda](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) pode fornecer um prontos para utilizar `.intunewin` ficheiro de formato para a instalação.

### <a name="use-powershell-to-create-custom-settings"></a>Utilize o PowerShell para criar definições personalizadas

Também pode utilizar o PowerShell para expandir capacidades de gestão de anfitriões. Este script de exemplo configura um plano de fundo padrão no anfitrião. É um recurso que também está disponível através do portal do Azure e perfis. O script de exemplo serve apenas para ilustrar a funcionalidade do PowerShell.

Poderá ter de configurar algumas definições e controles personalizados nas estações de trabalho seguras. Este exemplo muda o plano de fundo da estação de trabalho ao utilizar a capacidade do Powershell para identificar facilmente o dispositivo como uma estação de trabalho prontos a utilizar e seguro.

O [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) script do Microsoft Scripting Center permite que o Windows para carregá-lo [imagem de fundo gratuito e genérico](https://i.imgur.com/OAJ28zO.png) no arranque.

1. Transferir o script para um dispositivo local.
1. Atualize o customerXXXX e a localização de transferência da imagem em segundo plano. No nosso exemplo, substituímos customerXXXX para planos de fundo.  
1. Navegue para o **portal do Azure** > **Microsoft Intune** > **configuração do dispositivo** > **PowerShell scripts** > **Add**.
1. Fornecer um **Name** para o script e especifique a **localização do Script**.
1. Selecione **configurar**.
   1. Definir **executar este script utilizando o com sessão iniciada nas credenciais** ao **Sim**.
   1. Selecione **OK**.
1. Selecione **Criar**.
1. Selecione **atribuições** > **selecionar grupos**.
   1. Adicione o grupo de segurança **proteger estações de trabalho**.
   1. Selecione **Guardar**.

## <a name="enroll-and-validate-your-first-device"></a>Inscrever e validar o seu primeiro dispositivo

1. Para inscrever o seu dispositivo, terá das seguintes informações:
   * **Número de série** – apresentado no chassi do dispositivo.
   * **ID de produto do Windows** - encontrado em **System** > **sobre** no menu de definições do Windows.
   * Pode executar [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) para obter um hash de ficheiro CSV com todas as informações necessárias para inscrição de dispositivos.
   
     Executar `Get-WindowsAutoPilotInfo – outputfile device1.csv` para gerar as informações como um ficheiro CSV que pode ser importado no Intune.

     > [!NOTE]
     > O script exige direitos elevados. Ele é executado como remoto assinado. O `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` comando permite que o script seja executado corretamente.

   * Pode recolher estas informações ao iniciar sessão para uma versão do Windows 10 1809 ou o dispositivo superior. Seu revendedor de hardware também pode fornecer estas informações.
1. Na **portal do Azure**, aceda à **Microsoft Intune** > **inscrição de dispositivos** > **inscrição Windows**  >  **Dispositivos - dispositivos de gerir o Windows Autopilot**.
1. Selecione **importação** e escolha o seu ficheiro CSV.
1. Adicionar o dispositivo para o **proteger estações de trabalho** grupo de segurança.
1. Num dispositivo Windows 10 que pretende configurar, aceda a **definições do Windows** > **atualização e segurança** > **recuperação**.
   1. Escolher **começar** sob **repor este PC**.
   1. Siga as instruções de reposição e reconfigurar o dispositivo com as políticas de perfil e de conformidade configuradas.

Depois de ter configurado o dispositivo, concluir uma revisão e verifique a configuração. Confirme que o primeiro dispositivo está corretamente configurado antes de continuar a implementação.

## <a name="assign-and-monitor"></a>Atribuir e monitorizar

Para atribuir a dispositivos e utilizadores, tem de mapear a [selecionado perfis](https://docs.microsoft.com/intune/device-profile-assign) ao seu grupo de segurança. Todos os novos utilizadores que necessitam de permissões para o serviço tem de ser adicionados ao grupo de segurança.

Pode monitorizar perfis com [monitorização de perfil do Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Compreender [do Azure AD](https://docs.microsoft.com/azure/active-directory/index).
