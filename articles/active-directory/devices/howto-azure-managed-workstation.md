---
title: Implantar estações de trabalho - Azure Active Directory de geridas do Azure
description: Saiba como implementar a proteger estações de trabalho geridos pelo Azure para reduzir o risco de violação devido a configuração incorreta ou o comprometimento
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
ms.openlocfilehash: ae7c823b9aea262556081354a108ac9509a284ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110675"
---
# <a name="deploy-a-secure-workstation"></a>Implementar uma estação de trabalho segura

Agora que compreende [por que proteger o acesso de estação de trabalho é importante?](concept-azure-managed-workstation.md) chegou a hora para iniciar o processo de implantação usando as ferramentas disponíveis. Esta orientação irá utilizar os perfis definidos para criar uma estação de trabalho que é mais segura desde o início.

![Implementação de uma estação de trabalho segura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Antes de implementar a solução, tem de selecionar o perfil que irá utilizar. É importante ter em atenção que pode aplicar qualquer um dos perfis de selecionado e mover para outra ao atribuir o perfil no Intune com base nos seus requisitos. Vários perfis podem ser utilizados em simultâneo numa implementação e atribuídos através de atribuições da marca ou grupo.

| Perfil | Baixa | Melhorada | Alta | Especializada | Protegido | Isolado |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utilizador no Azure AD | Sim | Sim | Sim | Sim | Sim | Sim |
| Geridos pelo Intune | Sim | Sim | Sim | Sim | Sim | Sim |
| Dispositivo do Azure AD registado | Sim |  |  |  |  | |   |
| Dispositivo associado ao Azure AD |   | Sim | Sim | Sim | Sim | Sim |
| Base de segurança do Intune aplicada |   | Sim <br> (Avançado) | Sim <br> (HighSecurity) | Sim <br> (NCSC) | Sim <br> (Protegido) |  N/D |
| Hardware de cumprir os padrões de 10 seguro do Windows |   | Sim | Sim | Sim | Sim | Sim |
| Microsoft Defender ATP ativada |   | Sim  | Sim | Sim | Sim | Sim |
| Remoção de direitos de administrador |   |   | Sim  | Sim | Sim | Sim |
| Implementação com o Microsoft Autopilot |   |   | Sim  | Sim | Sim | Sim |
| Aplicações instaladas apenas pelo Intune |   |   |   | Sim | Sim |Sim |
| URLs restringidos a apenas lista aprovada |   |   |   | Sim | Sim |Sim |
| Internet (entrada/saída bloqueado) |   |   |   |  |  |Sim |

## <a name="license-requirements"></a>Requisitos de licença

Os conceitos abordados neste guia assumirá o Microsoft 365 Enterprise E5 ou um SKU equivalente. Algumas das recomendações neste guia podem ser implementadas com SKUs inferior. Pode encontrar informações adicionais sobre [licenciamento do Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Pode querer configurar [licenciamento baseado em grupo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) para os seus utilizadores automatizar o aprovisionamento de licenças.

## <a name="azure-active-directory-configuration"></a>Configuração do Active Directory do Azure

Configurar o seu diretório do Azure Active Directory (Azure AD), que irá gerir os seus utilizadores, grupos e dispositivos para suas estações de trabalho do administrador necessita que ative os serviços de identidade e de recursos com um [conta de administrador](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Quando cria a conta de administrador de estação de trabalho protegida, estiver expondo a conta para a estação de trabalho atual. Recomenda-se que fazer esta configuração inicial e todas as configurações globais de um dispositivo de segurança conhecido. Pode considerar as orientações para [impedir infeções de software maligno](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection) para reduzir o risco de expor, em primeiro lugar, a experiência de primeira contra ataques.

As organizações devem exigir autenticação multifator, pelo menos para seus administradores. Ver [implementar o MFA baseado na nuvem](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) para obter orientações de implementação.

### <a name="azure-ad-users-and-groups"></a>Grupos e utilizadores do Azure AD

No portal do Azure, navegue até **do Azure Active Directory** > **utilizadores** > **novo utilizador**. [Criar o seu utilizador de estação de trabalho seguro](https://docs.microsoft.com/Intune/quickstart-create-user), quem será o administrador do dispositivo.

* **Nome** -proteger o administrador de estação de trabalho
* **Nome de utilizador** - secure-ws-admin@identityitpro.com
* **Função de diretório** - **administrador limitado** e selecione a seguinte função administrativa
   * **Administrador do Intune**
* **Criar**

Iremos criar dois grupos de um para os utilizadores da estação de trabalho e para as estações de trabalho propriamente ditas. No portal do Azure, navegue até **do Azure Active Directory** > **grupos** > **novo grupo**

Primeiro grupo para os utilizadores da estação de trabalho. Pode querer configurar [licenciamento baseado em grupo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) para os utilizadores neste grupo para automatizar o provisionamento de licenças aos utilizadores.

* **Tipo de grupo** -segurança
* **Nome do grupo** -os utilizadores de estação de trabalho seguro
* **Tipo de associação** - atribuído
* Adicionar o seu utilizador de administrador de estação de trabalho segura ao grupo
   * secure-ws-admin@identityitpro.com
* Pode adicionar outros utilizadores que pretende gerir estações de trabalho seguras ao grupo
* **Criar**

Segundo grupo para dispositivos de estação de trabalho.

* **Tipo de grupo** -segurança
* **Nome do grupo** -proteger estações de trabalho
* **Tipo de associação** - atribuído
* **Criar**

### <a name="azure-ad-device-configuration"></a>Configuração de dispositivo do Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Especifique quem pode associar dispositivos ao Azure AD

Configurar os seus dispositivos definição no Active Directory para permitir que o seu grupo de segurança administrativa para associar dispositivos ao seu domínio. Para configurar esta definição no portal do Azure, navegue até **do Azure Active Directory** > **dispositivos** > **definições do dispositivo**. Escolher **Selected** sob **os utilizadores podem associar dispositivos ao Azure AD** e selecione o grupo "Utilizadores da estação de trabalho seguro".

#### <a name="removal-of-local-admin-rights"></a>Remoção de direitos de administrador local

Como parte da implementação do utilizadores de estações de trabalho do VIP, DevOps e estações de trabalho seguras ao nível terão sem direitos de administrador nos seus computadores. Para configurar esta definição no portal do Azure, navegue até **do Azure Active Directory** > **dispositivos** > **definições do dispositivo**. Selecione **None** sob **dispositivos associados de administradores locais adicionais no Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Exigir autenticação multifator para associar dispositivos

Para reforçar ainda mais o processo de associação de dispositivos para o Azure AD, navegue até **do Azure Active Directory** > **dispositivos** > **definições do dispositivo** escolher **Sim** sob **requerer o multi-Factor Auth para associar dispositivos** , em seguida, escolha **guardar**.

#### <a name="configure-mdm"></a>Configurar a MDM

No portal do Azure, navegue até **do Azure Active Directory** > **mobilidade (MDM e MAM)**  > **Microsoft Intune**. Alterar a definição **âmbito do utilizador MDM** ao **todos os** e escolha **guardar** como permitiremos que qualquer dispositivo para ser gerido pelo Intune nesse cenário. Mais informações podem ser encontradas no artigo [guia de introdução do Intune: Configurar a inscrição automática para dispositivos Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Iremos criar políticas de configuração e de conformidade do Intune num passo posterior.

#### <a name="azure-ad-conditional-access"></a>Acesso Condicional no Azure AD

De acesso condicional do AD do Azure pode ajudar a manter estas tarefas administrativas com privilégios em dispositivos em conformidade. Os utilizadores que definimos como membros do **proteger os utilizadores da estação de trabalho** grupo será necessário para efetuar a autenticação multifator ao iniciar sessão em aplicações na cloud. Iremos seguir as diretrizes de práticas recomendadas e excluir as nossas contas de acesso de emergência da política. Informações adicionais podem ser encontradas no artigo [gerir contas de acesso de emergência no Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

Para configurar o acesso condicional do portal do Azure, navegue para **do Azure Active Directory** > **acesso condicional** > **nova política**.

* **Nome** -seguro de dispositivos necessários política
* Atribuições
   * **Utilizadores e grupos**
      * Incluir - **utilizadores e grupos** – selecione o **proteger usuários de estação de trabalho** grupo que criou anteriormente
      * Excluir - **utilizadores e grupos** -selecione contas de acesso de emergência da sua organização
   * **Aplicações na cloud**
      * Incluir - **todas as aplicações na cloud**
* Controlos de acesso
   * **Concessão** - selecione **conceder acesso** botão de opção
      * **Exigir autenticação multifator**
      * **Exigir dispositivo seja marcado como compatível**
      * Para vários controles - **exigir todos os controlos selecionados**
* Ativar política - **no**

As organizações, opcionalmente, podem criar políticas para bloquear os países em que os utilizadores não seriam aceder a recursos da empresa. Obter mais informações sobre políticas de acesso condicional com base na localização de IP podem ser encontradas no artigo [o que é a condição de localização no Azure Active Directory condicional acesso?](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Configuração do Intune

### <a name="configure-enrollment-status"></a>Configurar o estado de inscrição

Conforme descrito na gestão de cadeias de fornecimento, garantindo a estação de trabalho segura é dispositivos fidedignos limpo é recomendado que quando a compra de novos dispositivos, que dispositivos ser definida como de fábrica [Windows 10 Pro no modo de S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), que limita a exposição e vulnerabilidades durante a gestão de cadeias de fornecimento. Depois de um dispositivo é recebido do seu fornecedor, o dispositivo será removido do modo de S através do Autopilot. As seguintes orientações fornecem detalhes sobre o processo de transformação a aplicar.

Queremos garantir que os dispositivos estão totalmente configurados antes de utilização. O Intune fornece um meio **bloqueie a utilização de dispositivos, até que todas as aplicações e perfis são instalados**. 

1. Partir do **portal do Azure** navegue para:
1. **Microsoft Intune** > **inscrição de dispositivos** > **inscrição Windows** > **página de estado de inscrição (pré-visualização)**  >  **Predefinido** > **definições**.
1. Definir **Mostrar o progresso de instalação do perfil de aplicação** ao **Sim**.
1. Definir **bloqueie a utilização de dispositivos, até que todas as aplicações e perfis são instalados** ao **Sim**.

### <a name="create-an-autopilot-deployment-profile"></a>Criar um perfil de implementação do Autopilot

Depois de criar um grupo de dispositivos, tem de criar um perfil de implementação, de modo a que pode configurar os dispositivos Autopilot.

1. No Intune no portal do Azure, selecione **inscrição de dispositivos** > **inscrição Windows** > **perfis de implementação**  >   **Criar perfil**.
1. Para nome, introduza **proteger o perfil de implementação de estação de trabalho**. Para a descrição, insira **implementação de estações de trabalho seguras**.
1. Conjunto de converter dispositivos visados tudo Autopilot como Yes. Esta definição certifica-se de que todos os dispositivos na lista registe-se com o serviço de implementação do Autopilot.  O processo de registo demora até 48 horas, pelo que deverá aguardar.
1. Para o modo de implementação, escolha **Self-implantação (pré-visualização)** . Dispositivos com este perfil estão associados com o utilizador a inscrição do dispositivo. Precisa de credenciais de utilizador para inscrever o dispositivo.
1. Na associação ao Azure AD como caixa, **associados ao Azure AD** deve ser escolhido e a cinzento.
1. Selecione a experiência de Out-of-box (OOBE), configure as seguintes opção e deixar que outras pessoas na sua predefinição e, em seguida, selecione **Ok**:
   1. Tipo de conta de utilizador: **Standard**
1. Selecione **Criar** para criar o perfil. O perfil de implementação do Autopilot está agora disponível para atribuir a dispositivos.
1. Escolher **atribuições** > **atribuir ao** > **grupos selecionados**
   1. **Selecionar grupos para incluir** -os utilizadores de estação de trabalho seguro

### <a name="configure-windows-update"></a>Configurar o Windows Update

Uma das coisas mais importantes, que uma organização pode fazer é manter o Windows 10 atualizados. Para manter o Windows 10 num estado seguro, vamos implementar uma cadência de atualização para gerir o ritmo a que as atualizações são aplicadas para estações de trabalho. Esta configuração pode ser encontrada na **portal do Azure** > **Microsoft Intune** > **atualizações de Software**  >  **Cadências de atualização do Windows 10**.

Iremos **criar** um novo anel de atualização com as seguintes definições em vez das predefinições.

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

Clique em **Create** , em seguida, no **atribuições** separador adicionar o **proteger estações de trabalho** grupo como um grupo incluído.

Informações adicionais sobre as políticas de atualização do Windows podem ser encontradas no [CSP de política - atualização](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Integração do Intune do Windows Defender ATP

Windows Defender ATP e o Microsoft Intune funcionam em conjunto para ajudar a evitar falhas de segurança e ajudar a limitar o impacto de falhas. As capacidades irão fornecer a deteção em tempo real. Também irá fornecer o ATP nossos auditoria detalhada de implantação e Registro em log os dispositivos de ponto de extremidade.

Para configurar a integração do Intune do Windows Defender ATP no portal do Azure, navegue até **Microsoft Intune** > **conformidade do dispositivo** > **do Windows Defender ATP** .

1. No passo 1 sob **configurar o Windows Defender ATP**, clique em **ligar o Windows Defender ATP ao Microsoft Intune no Centro de segurança do Windows Defender**.
1. No Centro de segurança do Windows Defender:
   1. Selecione **configurações** > **funcionalidades avançadas**
   1. Para **ligação do Microsoft Intune**, escolha **no**
   1. Selecione **salvar preferências**
1. Depois de uma ligação é estabelecida, volte ao Intune e clique em **atualizar** na parte superior.
1. Definir **ligar o Windows versão de dispositivos 10.0.15063 e superior para o Windows Defender ATP** para **no**.
1. **Guardar**

Informações adicionais podem ser encontradas no artigo [a proteção de ameaças avançada do Windows Defender](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="completing-hardening-of-the-workstation-profile"></a>Concluir a proteção do perfil de estação de trabalho

Para concluir com êxito o sistema de proteção da solução, transfira e execute o script com base em desejada **nível de perfil** da tabela a seguir.

| Perfil | Localização de transferência | Nome de ficheiro |
| --- | --- | --- |
| Segurança baixa | N/A |  N/A |
| Segurança Melhorada | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Alta segurança  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Especializada | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Conformidade especializadas * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Protegido | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

Conformidade especializadas * é um script que impõe a configuração especializada fornecida no SecurityBaseline de Windows 10 NCSC.

Assim que o script selecionado é executado com êxito, as atualizações para as políticas de perfis e podem ser feitas no Microsoft Intune. Os scripts para perfis avançado e seguro criam políticas e perfis para, mas devem atribuir a política para o seu **proteger estações de trabalho** grupo.

* Perfis de configuração de dispositivos do Intune criados pelos scripts podem ser encontrados no **portal do Azure** > **Microsoft Intune** > **deconfiguraçãododispositivo**  >  **Perfis**.
* Políticas de conformidade do dispositivo do Intune criadas pelos scripts podem ser encontradas no **portal do Azure** > **Microsoft Intune** > **conformidade do dispositivo**  >  **Políticas**.

Para rever as alterações também pode exportar os perfis e aplicar alterações ao ficheiro de exportação, conforme descrito no SECCON documentação com base no e adicional de proteção que é necessária.

Os dados do Intune a executar a exportar o script `DeviceConfiguration_Export.ps1` do [DeviceConfiguration GiuHub repositório](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) fornecerá a exportação atual de todos os perfis do Intune existentes.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurações adicionais e proteção a considerar

As orientações fornecidas tem ativado uma estação de trabalho protegida, controlos adicionais também devem ser considerados, como aceder a navegadores alternativos, saídos HTTP permitidas e bloqueadas Web sites e a capacidade de executar o script de PowerShell personalizado.

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>Regras de entrada e saídas restritivas no fornecedor de serviços de configuração do firewall (CSP)

Gestão adicional de regras de entrada e de saída pode ser atualizado para refletir os pontos finais do permitidos e bloqueados. À medida que Continuamos proteger a estação de trabalho segura, podemos mover a restrição para uma Negar tudo entrada e saída como padrão e adicionar sites permitidos para a saída refletir a sites comuns e fidedignos. As informações de configuração adicionais para o fornecedor de serviço de configuração de Firewall podem ser encontradas no artigo [Firewall CSP](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Recomendações de padrão restringida são:

* Negar toda a entrada
* Negar toda a saída

O projeto de Spamhaus mantém uma lista de bom que as organizações podem utilizar como ponto de partida para bloquear sites conhecidos como [a lista de blocos de domínio (DBL)](https://www.spamhaus.org/dbl/).

### <a name="managing-local-applications"></a>Gestão de aplicações locais

Remover aplicativos locais, incluindo a remoção de aplicativos de produtividade irá mover a estação de trabalho segura para um Estado verdadeiramente protegido. No nosso exemplo, iremos adicionar Chrome como browser predefinido e restringir a capacidade de modificar o navegador, incluindo plug-ins através do Intune.

#### <a name="deploy-applications-using-intune"></a>Implementar aplicações com o Intune

Em algumas situações, os aplicativos como o browser Google Chrome são necessários na estação de trabalho protegida. O exemplo a seguir fornece instruções para instalar o Chrome para dispositivos no grupo de segurança **proteger estações de trabalho** criado anteriormente.

1. Transferir o instalador offline [pacote Chrome para 64‑bit do Windows](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Extraia os ficheiros e tome nota da localização do `GoogleChromeStandaloneEnterprise64.msi` para instalar com o Intune
1. Na **portal do Azure** procure **Microsoft Intune** > **aplicações de cliente** > **aplicações**  >  **Adicionar**
1. Sob **tipo de aplicação**, escolha **Line-of-business**
1. Sob **o ficheiro de pacote de aplicação**, selecione a `GoogleChromeStandaloneEnterprise64.msi` na localização extraída e clique **OK**
1. Sob **informações da aplicação**, forneça uma descrição e o publicador e escolha **OK**
1. Clique em **adicionar**
1. Sobre o **atribuições** selecionar **disponível para dispositivos inscritos** sob **tipo de atribuição**
1. Sob **grupos incluídos**, adicione o **proteger estações de trabalho** grupo que criou anteriormente
1. Clique em **OK** , em seguida, **guardar**

Orientações adicionais sobre como configurar as definições do Chrome podem ser encontradas no seu artigo de suporte [gerir Browser do Chrome com o Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurar o portal da empresa para aplicações personalizadas

Num modo seguro, instalação de aplicativos serão restringida para o portal da empresa do Intune. No entanto, se instalar o portal requer acesso ao Microsoft Store. Em nossa solução segura, faremos o portal disponíveis para todos os dispositivos com o modo offline do portal da empresa.

Instalar uma Intune gerido cópia dos [Portal da empresa](https://docs.microsoft.com/Intune/store-apps-company-portal-app) irá permitir a capacidade de disponibilizar ferramentas adicionais a pedido para os utilizadores da estação de trabalho protegida.

Algumas organizações podem ser necessários para instalar aplicações do Windows de 32 bits ou aplicações que necessitam de outras preparações a implementar. Para estas aplicações, o [ferramenta de preparação do Microsoft win32 conteúda](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) fornecerá um pronto a utilizar `.intunewin` ficheiro de formato para a instalação.

### <a name="setting-up-custom-settings-using-powershell"></a>Configurar definições personalizadas com o PowerShell

Também utilizaremos um exemplo de utilização do PowerShell para fornecer extensibilidade no gerenciamento de host. O script irá configurar um plano de fundo padrão no anfitrião. Esta capacidade também está disponível nos perfis e só é utilizada para ilustrar o recurso.

Na solução pode haver a necessidade de configurar alguns controles personalizados e as definições nas estações de trabalho seguras. No nosso exemplo, iremos alterar o plano de fundo da estação de trabalho com o Powershell para poder identificar facilmente o dispositivo como uma estação de trabalho segura pronta para ser utilizado. Embora nosso exemplo irá utilizar o PowerShell para concluir essa tarefa, também pode ser concluída no portal do Azure.

Nosso exemplo, vai utilizar o seguinte procedimento [imagem de fundo genérico gratuita](https://i.imgur.com/OAJ28zO.png) e o [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) do Microsoft Scripting Center para permitir que o Windows carregar o plano de fundo no início.

1. Transferir o script para um dispositivo local
1. Atualize o customerXXXX e a localização de transferência do plano de fundo que deseja para utilizar no script para refletir o arquivo em segundo plano e a pasta que gostaria que a implementação a utilizar. No nosso exemplo, substituímos customerXXXX para planos de fundo.  
1. Navegue para o **portal do Azure** > **Microsoft Intune** > **configuração do dispositivo** > **PowerShell scripts** > **adicionar**
1. Fornecer um **Name** para o script e especifique a **localização do Script** onde transferiu para
1. Selecione **configurar**
   1. Definir **executar este script utilizando o com sessão iniciada nas credenciais**ao **Sim**
   1. Clique em **OK**
1. Clique em **Criar**
1. Selecione **atribuições** > **selecionar grupos**
   1. Adicione o grupo de segurança **proteger estações de trabalho** criado anteriormente e clique em **guardar**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>Utilização da pré-visualização: Linha de base de segurança MDM de Outubro de 2018

Microsoft Intune introduziu a funcionalidade de gestão de linha de base de segurança dando aos administradores uma forma simples de impor uma postura de segurança de linha de base comum. A linha de base fornece um meio semelhante para alcançar um bloqueado para baixo de estação de trabalho de perfil de avançado.

Para a estação de trabalho segura, implementação que esta linha de base não é utilizada como ele irá entrar em conflito com a implementação de uma configuração segura.

|   |   |   |
| :---: | :---: | :---: |
| Acima de bloqueio | Instalação de dispositivo | Serviços de Ambiente de Trabalho Remoto |
| Tempo de execução da aplicação | Bloqueio do dispositivo | Gestão remota |
| Gestão de Aplicações | Serviço de registo de eventos | Chamada de procedimento remoto |
| Reprodução automática | Experiência | Pesquisa |
| BitLocker | Exploit Guard | Smart Screen |
| Browser | Explorador de ficheiros | Requisito de sistema|
| Conectividade | Internet Explorer | Wi-Fi |
| Delegação de credenciais | Opções de segurança de políticas locais | Gestor de ligações do Windows |
| Credenciais da interface do Usuário | Guia de segurança do MS | Windows Defender|
| Proteção de Dados | MSS legado | Área de trabalho do Windows Ink |
| Device Guard | Power | Windows PowerShell |

Obter mais informações sobre esta funcionalidade de pré-visualização podem ser encontradas no artigo [as configurações da linha de base de segurança do Windows para o Intune](https://docs.microsoft.com/Intune/security-baseline-settings-windows).

## <a name="enroll-and-validate-your-first-device"></a>Inscrever e validar o seu primeiro dispositivo

1. Para inscrever o seu dispositivo, terá das seguintes informações:
   * **Número de série** – apresentado nos chassis de dispositivos
   * **ID de produto do Windows** - encontrado em **System** > **sobre** no menu de definições do Windows.
   * Em execução [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) fornecerá um hash de ficheiro CSV para inscrição de dispositivos com todas as informações necessárias. 
      * Executar `Get-WindowsAutoPilotInfo – outputfile device1.csv` para gerar as informações como um ficheiro CSV que pode ser importado para o Intune.

   > [!NOTE]
   > O script irá exigir direitos elevados e executar como remoto assinado. Pode utilizar o seguinte comando para permitir que o script seja executado corretamente. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  Pode recolher estas informações ao iniciar sessão para uma versão do Windows 10 1809 ou o dispositivo superior para reunir as informações ou o revendedor de hardware pode fornecer estas informações ao solicitar novos dispositivos.
1. Recolher as informações necessárias e retornar para o **portal do Azure**. Navegue para **Microsoft Intune** > **inscrição de dispositivos** > **inscrição Windows** > **dispositivos - Gerir dispositivos Windows Autopilot**, selecione **importação**e escolha o ficheiro CSV criado ou foram fornecidos.
1. Adicionar o dispositivo inscrito agora para o grupo de segurança **proteger estações de trabalho** criado anteriormente.
1. Do dispositivo Windows 10 que pretende configurar, navegue até **definições do Windows** > **atualização e segurança** > **recuperação**. Escolher **começar** sob **repor este PC** e siga as instruções de reposição e reconfigurar o dispositivo com as políticas de perfil e de conformidade configuradas.

Depois do dispositivo tiver sido configurado, concluir uma revisão e verifique a configuração. Certifique-se que o primeiro dispositivo está configurado corretamente antes de continuar a implementação.

## <a name="assignment-and-monitoring"></a>Monitorização e de atribuição

Atribuição de dispositivo e os usuários exigirão o mapeamento do [selecionado perfis](https://docs.microsoft.com/intune/device-profile-assign) para a segurança do grupo e todos os novos utilizadores que terá permissão para o serviço será necessários para ser adicionado ao grupo de segurança.

Monitorizar os perfis para pode ser feito usando o monitoramento [perfis do Microsoft Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Passos Seguintes

[Microsoft Intune](https://docs.microsoft.com/intune/index) documentação

[O Azure AD](https://docs.microsoft.com/azure/active-directory/index) documentação