---
title: Implante postos de trabalho geridos pelo Azure - Diretório Ativo Azure
description: Aprenda a implementar estações de trabalho seguras e geridas pelo Azure para reduzir o risco de violação devido a uma configuração errada ou um compromisso.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672612"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Implementar uma estação de trabalho segura e gerida pelo Azure

Agora que [compreende sesta de trabalho segura,](concept-azure-managed-workstation.md)é hora de iniciar o processo de implantação. Com esta orientação, você usa perfis definidos para criar uma estação de trabalho mais segura desde o início.

![Implantação de uma estação de trabalho segura](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Selecione um perfil antes de implementar a solução. Pode utilizar múltiplos perfis simultaneamente numa implementação e atribuí-los com etiquetas ou grupos.

> [!NOTE]
> Aplique qualquer um dos perfis conforme necessário pelos seus requisitos. Pode mover-se para outro perfil atribuindo-o no Microsoft Intune.

| Perfil | Baixa | Melhorada | Alta | Especializada | Seguro | Isolado |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Utilizador em Azure AD | Sim | Sim | Sim | Sim | Sim | Sim |
| Gerido intune | Sim | Sim | Sim | Sim | Sim | Sim |
| Dispositivo - Azure AD registado | Sim |  |  |  |  | |   |
| Dispositivo - Azure AD juntou-se |   | Sim | Sim | Sim | Sim | Sim |
| Linha de base de segurança intonizada aplicada |   | Sim <br> (Melhorado) | Sim <br> (Alta Segurança) | Sim <br> (NCSC) | Sim <br> (Seguro) | ND |
| Hardware cumpre padrões seguros do Windows 10 |   | Sim | Sim | Sim | Sim | Sim |
| Microsoft Defender ATP ativado |   | Sim  | Sim | Sim | Sim | Sim |
| Remoção dos direitos administrativos |   |   | Sim  | Sim | Sim | Sim |
| Implementação utilizando o Microsoft Autopilot |   |   | Sim  | Sim | Sim | Sim |
| Apps instaladas apenas pela Intune |   |   |   | Sim | Sim |Sim |
| URLs restritos à lista aprovada |   |   |   | Sim | Sim |Sim |
| Internet bloqueada (entrada/saída) |   |   |   |  |  |Sim |

> [!NOTE]
> Nos dispositivos de orientação de estações de trabalho seguros serão **atribuídos** com perfis e políticas. Os utilizadores não terão as políticas aplicadas diretamente aos mesmos, permitindo que a partilha de dispositivos (dispositivos partilhados) esteja em vigor. Se uma estação de trabalho segura não for partilhada na sua implementação, ou forem necessárias políticas individuais de utilizador, a atribuição dos perfis de política do utilizador pode ser atribuída ao utilizador e ao dispositivo. 

## <a name="license-requirements"></a>Requisitos de licença

Os conceitos abrangidos por este guia assumem que tem o Microsoft 365 Enterprise E5 ou um SKU equivalente. Algumas das recomendações deste guia podem ser implementadas com SKUs mais baixos. Para mais informações, consulte o licenciamento da [Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Para automatizar o fornecimento de licenças, considere [o licenciamento baseado em grupo](../users-groups-roles/licensing-groups-assign.md) para os seus utilizadores.

## <a name="azure-active-directory-configuration"></a>Configuração do Diretório Ativo Azure

O Azure Ative Directory (Azure AD) gere utilizadores, grupos e dispositivos para os seus postos de trabalho de administrador. Ativar serviços de identidade e funcionalidades com uma [conta de administrador.](../users-groups-roles/directory-assign-admin-roles.md)

Ao criar a conta de administrador de estações de trabalho segura, expõe a conta à sua estação de trabalho atual. Certifique-se de que utiliza um dispositivo seguro conhecido para fazer esta configuração inicial e toda a configuração global. Para reduzir a exposição ao ataque pela primeira vez, considere seguir as [orientações para prevenir infeções por malware](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Exija a autenticação de vários fatores, pelo menos para os seus administradores. Consulte o [MFA baseado em nuvem](../authentication/howto-mfa-getstarted.md) para obter orientação de implementação.

### <a name="azure-ad-users-and-groups"></a>Utilizadores e grupos de AD Azure

1. A partir do portal Azure, navegue até **ao Azure Ative Directory** > **Users** > **Novo utilizador.**
1. Crie o administrador do seu dispositivo seguindo os passos no tutorial de [criação](/Intune/quickstart-create-user)do utilizador .
1. Introduza:

   * **Nome** - Administrador de estação de trabalho seguro
   * **Nome do utilizador** - `secure-ws-admin@identityitpro.com`
   * **Papel** - de diretório**Administrador limitado** e selecione a função de **Administrador Intune.**

1. Selecione **Criar**.

Em seguida, cria-se dois grupos: utilizadores de estações de trabalho e dispositivos de estação de trabalho.

A partir do portal Azure, navegue até **ao Azure Ative Directory** > **Groups** > **Novo grupo.**

1. Para o grupo de utilizadores da estação de trabalho, é possível configurar [o licenciamento baseado em grupo](../users-groups-roles/licensing-groups-assign.md) para automatizar o fornecimento de licenças aos utilizadores.
1. Para o grupo de utilizadores da estação de trabalho, introduza:

   * **Tipo de grupo** - Segurança
   * **Nome do grupo** - Utilizadores seguros da estação de trabalho
   * **Tipo de adesão** - Atribuído

1. Adicione o utilizador seguro do administrador da estação de trabalho:`secure-ws-admin@identityitpro.com`
1. Pode adicionar quaisquer outros utilizadores que estejam a gerir estações de trabalho seguras.
1. Selecione **Criar**.
1. Para o grupo de dispositivos de estação de trabalho, introduza:

   * **Tipo de grupo** - Segurança
   * **Nome do grupo** - Estações de Trabalho Seguras
   * **Tipo de adesão** - Atribuído

1. Selecione **Criar**.

### <a name="azure-ad-device-configuration"></a>Configuração do dispositivo Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Especifique quem pode aderir aos dispositivos à Azure AD

Configure a definição dos seus dispositivos no Diretório Ativo para permitir que o seu grupo de segurança administrativa se junte aos dispositivos ao seu domínio. Para configurar esta definição a partir do portal Azure:

1. Vá às**Devices** > **definições**do dispositivo de **diretório** > ativo Azure .
1. Escolher **selecionado** em **Utilizadores pode juntar dispositivos ao Azure AD**e, em seguida, selecionar o grupo "Utilizadores de Estação de Trabalho Seguros".

#### <a name="removal-of-local-admin-rights"></a>Remoção dos direitos administrativos locais

Este método requer que os utilizadores dos postos de trabalho VIP, DevOps e de nível seguro não tenham direitos de administrador nas suas máquinas. Para configurar esta definição a partir do portal Azure:

1. Vá às**Devices** > **definições**do dispositivo de **diretório** > ativo Azure .
1. Selecione **Nenhum** sob **administradores locais adicionais em dispositivos ligados a Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Exigir autenticação de vários fatores para aderir a dispositivos

Para reforçar ainda mais o processo de junção de dispositivos à Azure AD:

1. Vá às**Devices** > **definições**do dispositivo de **diretório** > ativo Azure .
1. **Selecione Sim** sob **exigir multi-factor Auth para juntar dispositivos**.
1. Selecione **Guardar**.

#### <a name="configure-mobile-device-management"></a>Configure a gestão de dispositivos móveis

Do portal Azure:

1. Navegue até **à Azure Ative Directory** > **Mobility (MDM e MAM)** > **Microsoft Intune**.
1. Altere a definição de âmbito de **utilização** do MDM para **Todos**.
1. Selecione **Guardar**.

Estes passos permitem-lhe gerir qualquer dispositivo com Intune. Para mais informações, consulte [Intune Quickstart: Instale a inscrição automática para dispositivos Windows 10](/Intune/quickstart-setup-auto-enrollment). Cria políticas de configuração e conformidade intune num passo futuro.

#### <a name="azure-ad-conditional-access"></a>Acesso Condicional Azure AD

O Acesso Condicional da AD Azure pode ajudar a restringir tarefas administrativas privilegiadas a dispositivos conformes. Os membros pré-definidos do grupo Utilizadores de **Estações** de Trabalho Seguras são obrigados a realizar a autenticação de vários fatores ao iniciar a sessão nas aplicações cloud. A melhor prática é excluir as contas de acesso de emergência da política. Para mais informações, consulte Gerir contas de [acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md).

## <a name="intune-configuration"></a>Configuração insintonizada

### <a name="configure-enrollment-status"></a>Configurar o estado da inscrição

É importante garantir que o seu posto de trabalho seguro é um dispositivo limpo de confiança. Ao adquirir novos dispositivos, pode insistir que estão definidos para o [Windows 10 Pro no modo S](/Windows/deployment/Windows-10-pro-in-s-mode)– o que limita a exposição a vulnerabilidades durante a gestão da cadeia de fornecimento. Depois de receber um dispositivo do seu fornecedor, pode utilizar o Autopilot para o alterar do modo S. A seguinte orientação fornece detalhes sobre a aplicação do processo de transformação.

Para garantir que os dispositivos estão totalmente configurados antes da utilização, o Intune fornece um meio para bloquear a utilização do dispositivo até que **todas as aplicações e perfis estejam instalados**.

Do **portal Azure:**

1. Vá para o **Microsoft Intune** > **Device inscrição** > Do Windows Configurações**de predefinição** > **Settings**do > estado de**inscrição** > **Enrollment Status Page**.
1. Definir Mostrar o progresso da instalação do perfil da **aplicação** para **Sim**.
1. Delineie a utilização do dispositivo Block até que **todas as aplicações e perfis estejam instalados** no **Sim**.

### <a name="create-an-autopilot-deployment-profile"></a>Criar um perfil de implementação do Autopilot

Depois de criar um grupo de dispositivos, deve criar um perfil de implementação para configurar os dispositivos Autopilot.

Intune no portal Azure:

1. Selecione os > perfis de**implementação**de > **inscrição** **Device enrollment** > do Windows no dispositivo**Criar perfil**.
1. Introduza:

   * Nome - **Perfil de implantação seguro da estação**de trabalho .
   * Descrição - **Implantação de postos de trabalho seguros**.
   * Defina **Converter todos os dispositivos visados para o Autopilot** como **Sim**. Esta definição garante que todos os dispositivos na lista são registados com o serviço de implementação do Autopilot. O processo de registo demora até 48 horas, pelo que deverá aguardar.

1. Selecione **Next**.

   * Para o modo de **implantação,** escolha **a auto-implantação (pré-visualização)**. Os dispositivos com este perfil estão associados ao utilizador que inscreveu o dispositivo. Precisa de credenciais de utilizador para inscrever o dispositivo. É essencial notar que a implementação de um dispositivo no modo **de auto-implantação** permitirá implementar portáteis num modelo partilhado. Nenhuma atribuição de utilizador irá acontecer até que o dispositivo seja atribuído a um utilizador pela primeira vez. Como resultado, quaisquer políticas de utilizador como o BitLocker não serão ativadas até que uma atribuição de utilizador esteja concluída. Para obter mais informações sobre como iniciar sessão num dispositivo seguro, consulte [perfis selecionados](/intune/device-profile-assign).
   * O **Join to Azure AD como** caixa deve mostrar **Azure AD juntou-se** e ser cinza.
   * Selecione o seu Padrão de Idioma (Região), **tipo**de conta de utilizador . 

1. Selecione **Next**.

   * Selecione uma etiqueta de âmbito se tiver configurado uma.

1. Selecione **Next**.
1. Escolha **atribuições** > **a** > **grupos selecionados**. Em **grupos Selecionados para incluir,** escolha **Postos de Trabalho Seguros**.
1. Selecione **Next**.
1. Selecione **Criar** para criar o perfil. O perfil de implementação do Autopilot está agora disponível para atribuir a dispositivos.

A inscrição no dispositivo em Autopilot proporciona uma experiência de utilizador diferente com base no tipo e função do dispositivo. No nosso exemplo de implementação, ilustramos um modelo em que os dispositivos protegidos são implantados a granel e podem ser partilhados, mas quando utilizados pela primeira vez, o dispositivo é atribuído a um utilizador. Para mais informações, consulte a [inscrição do dispositivo Intune Autopilot](/intune/device-enrollment).

### <a name="configure-windows-update"></a>Configure atualização do Windows

Manter o Windows 10 atualizado é uma das coisas mais importantes que pode fazer. Para manter o Windows num estado seguro, implementa um anel de atualização para gerir o ritmo que as atualizações são aplicadas aos postos de trabalho. 

Esta orientação recomenda que crie um novo anel de atualização e altere as seguintes definições predefinidas:

No portal do Azure:

1. Vá ao **Microsoft Intune** > **Software atualiza** > **os Windows 10 Update Rings**.
1. Introduza:

   * Nome - **Atualizações de estação de trabalho geridas pelo Azure**
   * Canal de manutenção - **Windows Insider - Fast**
   * Diferimento de atualização de qualidade (dias) - **3**
   * Período de diferimento da atualização de funcionalidades (dias) - **3**
   * Comportamento de atualização automática - **Instalação automática e reinicialização sem controlo do utilizador final**
   * Bloquear o utilizador de pausas nas atualizações do Windows - **Bloquear**
   * Exigir a aprovação do utilizador para reiniciar fora do horário de trabalho - **Obrigatório**
   * Permitir que o utilizador reinicie (reiniciar ativado) - **Obrigatório**
   * Utilizadores de transição para reiniciar ativado após um reinício automático (dias) - **3**
   * Lembrete de reinício (dias) - **3**
   * Definir prazo para reinícios pendentes (dias) - **3**

1. Selecione **Criar**.
1. No separador **De atribuição,** adicione o grupo **"Estações de Trabalho Seguras".**

Para obter mais informações sobre as políticas de Atualização do Windows, consulte [Política CSP - Update](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integração ATP Intune do Windows Defender

O Windows Defender ATP e o Microsoft Intune trabalham em conjunto para ajudar a evitar falhas de segurança. Podem também limitar o impacto das infrações. As capacidades ATP fornecem deteção de ameaças em tempo real, bem como permitem uma auditoria e exploração extensiva dos dispositivos de ponta.

Para configurar a integração do Windows Defender ATP e Intune, vá ao portal Azure.

1. Navegue para **microsoft Intune** > **Device compliance** > **Windows Defender ATP**.
1. No passo 1 sob configuração do **Windows Defender ATP,** selecione Ligar o Windows **Defender ATP à Microsoft Intune no Windows Defender Security Center**.
1. No Centro de Segurança do Windows Defender:

   1. Selecione **Definições** > **Características avançadas**.
   1. Para **a ligação Microsoft Intune,** escolha **On**.
   1. Selecione **Guardar preferências**.

1. Depois de estabelecida uma ligação, volte a Intune e selecione **Refresh** na parte superior.
1. Defina a opção **Ligar dispositivos Windows com versão 10.0.15063 e posterior a Windows Defender ATP** para **Ativado**.
1. Selecione **Guardar**.

Para mais informações, consulte [o Windows Defender Advanced Threat Protection](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Terminar o endurecimento do perfil da estação de trabalho

Para completar com sucesso o endurecimento da solução, descarregue e execute o script apropriado. Encontre os links de descarregamento para o **nível**de perfil desejado:

| Perfil | Descarregue a localização | Nome de ficheiro |
| --- | --- | --- |
| Baixa Segurança | N/D | N/D |
| Segurança Reforçada | https://aka.ms/securedworkstationgit | Estação de Trabalho Melhorada-Windows10-(1809).ps1 |
| Alta Segurança | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Especializada | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Conformidade Especializada* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Seguro | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*Compliance Especializada é um script que aplica a configuração especializada fornecida na Base de Segurança NCSC Windows10.

Depois de o script ser executado com sucesso, pode fazer atualizações para perfis e políticas em Intune. Os scripts para perfis Melhorados e Seguros criam políticas e perfis para si, mas deve atribuir a política ao seu grupo de dispositivos **Secure Workstations.**

* Aqui é onde você pode encontrar os perfis de configuração do dispositivo Intune criados pelos scripts: **Azure portal** > **Microsoft Intune** > Device Perfis**de****configuração** > .
* Aqui é onde você pode encontrar as políticas de conformidade do dispositivo Intune criadas pelos scripts: **Portal Azure** > **Microsoft Intune** > **Device Compliance** > **Policies**.

Para rever as alterações feitas pelos scripts, pode exportar os perfis. Desta forma, pode determinar o endurecimento adicional que pode ser exigido conforme descrito na documentação da [SECCON](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Execute o script `DeviceConfiguration_Export.ps1` de exportação de dados Intune do [repositório GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) de Configuração de Dispositivos para exportar todos os perfis intune atuais.

## <a name="additional-configurations-and-hardening-to-consider"></a>Configurações adicionais e endurecimento a considerar

Seguindo a orientação aqui, implementou uma estação de trabalho segura. No entanto, também deve considerar controlos adicionais. Por exemplo:

* limitar o acesso a navegadores alternativos
* permitir a saída HTTP
* bloquear sites selecionados
* definir permissões para executar scripts powerShell personalizados

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Definir regras no fornecedor de serviços de configuração de firewall (CSP)

Pode efazer alterações adicionais na gestão das regras de entrada e saída, conforme necessário para os seus pontos finais permitidos e bloqueados. À medida que continua a endurecer o posto de trabalho seguro, pode afrouxar a restrição que nega todo o tráfego de entrada e saída. Pode adicionar sites de saída autorizados para incluir sites comuns e de confiança. Para mais informações, consulte o serviço de [configuração firewall](/Windows/client-management/mdm/firewall-csp).

A gestão restritiva do tráfego de URL inclui:

* Negar todo o tráfego de entrada - Definido no script de perfil de estação de trabalho seguro.
* Negar todos os serviços de saída, exceto os serviços azure e microsoft selecionados, incluindo o Azure Cloud Shell e a capacidade de permitir o Reset de Palavra-passe Azure.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Se procura fornecer mais granularidade às suas regras de bloqueio, pode consultar o Projeto Spamhaus que mantém a Lista de Blocos de [Domínio (DBL)](https://www.spamhaus.org/dbl/): um bom recurso para usar como um conjunto avançado de regras para implementar para bloquear sites.

### <a name="manage-local-applications"></a>Gerir aplicações locais

A estação de trabalho segura muda-se para um estado verdadeiramente endurecido quando as aplicações locais são removidas, incluindo aplicações de produtividade. Aqui, adiciona o Chrome como navegador padrão e utiliza o Intune para limitar a capacidade de um utilizador modificar o navegador e os seus plug-ins.

#### <a name="deploy-applications-using-intune"></a>Implementar aplicações usando intune

Em algumas situações, aplicações como o navegador Google Chrome são necessárias na estação de trabalho segura. O exemplo que se segue fornece instruções para instalar o Chrome em dispositivos do grupo de segurança **Secure Workstations**.

1. Descarregue o pacote Chrome do instalador offline [para windows 64-bits](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extraios os ficheiros e tome nota da localização do `GoogleChromeStandaloneEnterprise64.msi` ficheiro.
1. No **portal Azure** navegue pelas**aplicações** >  > do **Microsoft Intune** > **Client.****Add**
1. No **tipo de App,** escolha **Linha de Negócios.**
1. No ficheiro de pacote `GoogleChromeStandaloneEnterprise64.msi` **app,** selecione o ficheiro a partir do local extraído e selecione **OK**.
1. De acordo **com as informações da App,** forneça uma descrição e um editor. Selecione **OK**.
1. Selecione **Adicionar**.
1. No separador **Atribuições,** selecione **Disponível para dispositivos matriculados** sob **o tipo**de Atribuição .
1. Em **grupos incluídos,** adicione o grupo **Detrabalho Seguro.**
1. Selecione **OK**, e, em seguida, selecione **Guardar**.

Para obter mais informações sobre a configuração das definições do Chrome, consulte Gerir o Navegador Chrome com o [Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Configurar o portal da empresa para aplicações personalizadas

Num modo seguro, a instalação da aplicação está restrita ao portal da empresa Intune. No entanto, a instalação do portal requer acesso à Microsoft Store. Na sua solução segura, pode disponibilizar o portal da empresa a todos os dispositivos através de um modo offline.

Uma cópia gerida pelo Intune do Portal da [Empresa](/Intune/store-apps-company-portal-app) dá-lhe acesso a pedido a ferramentas adicionais que pode empurrar para os utilizadores das estações de trabalho seguras.

Poderá ter de instalar aplicações do Windows de 32 bits ou outras aplicações cuja implementação requer preparações especiais. Nestes casos, a ferramenta de preparação de [conteúdo win32 da Microsoft](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) pode fornecer um ficheiro de formato pronto a utilizar `.intunewin` para a instalação.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Acesso Condicional apenas permite garantir capacidade de estação de trabalho para aceder ao portal Azure

A Azure AD oferece a capacidade de gerir e restringir, quem e o que pode aceder ao seu portal de gestão de nuvem Azure. Ativar o [Acesso Condicional](../conditional-access/overview.md) assegurará que apenas a sua estação de trabalho segura pode gerir ou alterar recursos. É essencial que, ao implementar esta funcionalidade, considere, se a funcionalidade de acesso de [emergência](../users-groups-roles/directory-emergency-access.md) pode ou deve ser usada apenas para casos extremos e a conta gerida através da política.

> [!NOTE]
> Terá de criar um grupo de utilizadores e incluir o utilizador de emergência que possa contornar a política de Acesso Condicional. Para o nosso exemplo, temos um grupo de segurança chamado **Emergency BreakGlass**

1. Navegue no **portal** > Azure**Microsoft Intune** > **Acesso Condicional - Políticas** > **Nova Política**.
1. Forneça um **nome** para a apólice.
1. Selecione **Utilizadores e Grupos** > **Selecione utilizadores e grupos** 
1. **Selecione incluir** > **funções de Diretório** > Escolha as funções > Administrador Global, Administrador de Funções Privilegiadas, Administrador de Autenticação Privilegiada, Administrador de Segurança, Administrador de Conformidade, Administrador de Acesso Condicional, Administrador de Aplicações, Administrador de Aplicações na Nuvem, Administrador de Serviço Intune
1. **Selecione Excluir** > Escolha **utilizadores e grupos** > **Selecione os utilizadores excluídos** > Selecione o seu grupo **BreakGlass de emergência.**
1. Selecione **aplicações ou ações da Cloud** > Selecione todas as **aplicações em nuvem**
1. Selecione **condições** > selecione as plataformas de **dispositivos** > escolha configurar **sim** > **selecione as plataformas** de dispositivos escolha o **Windows**
1. Selecione **controlos de acesso** > Selecione Acesso de **concessão** **Sim** > Escolha Exigir que o dispositivo seja marcado **como conforme**. 
1. Selecione **ativar a política** > **em**
 
Este conjunto de políticas irá garantir que os seus Administradores devem utilizar um dispositivo Windows compatível, que é definido por Intune e WDATP. 

As organizações devem também considerar o bloqueio dos protocolos de autenticação legado nos seus ambientes. Existem várias formas de realizar esta tarefa, para mais informações sobre o bloqueio de protocolos de autenticação legado ver o artigo, [Como: Bloquear a autenticação legado ao Azure AD com Acesso Condicional](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Use powerShell para criar configurações personalizadas

Também pode utilizar o PowerShell para alargar as capacidades de gestão do anfitrião. Este script de exemplo configura um fundo padrão no hospedeiro. É uma capacidade que também está disponível através do portal Azure e perfis. O roteiro de exemplo serve apenas para ilustrar a funcionalidade PowerShell.

Poderá ser necessário configurar alguns controlos e definições personalizados nos seus postos de trabalho seguros. Este exemplo altera o pano de fundo da estação de trabalho utilizando a capacidade da Powershell de identificar facilmente o dispositivo como uma estação de trabalho pronta a usar e segura.

O script [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) do Microsoft Scripting Center permite que o Windows carregue esta [imagem de fundo gratuita e genérica](https://i.imgur.com/OAJ28zO.png) no arranque.

1. Descarregue o script para um dispositivo local.
1. Atualize o clienteXXXX e a localização de descarregamento da imagem de fundo. No nosso exemplo, substituímos o clienteXXXX por fundos.
1. Navegue no **portal** > Azure**Microsoft Intune** > **Device configuração** > **PowerShell scripts** > **Add**.
1. Forneça um **nome** para o script e especifique a localização do **Script**.
1. Selecione **Configurar**.
   1. Definir **Executar este script utilizando as credenciais registadas** para **Sim**.
   1. Selecione **OK**.
1. Selecione **Criar**.
1. Selecione **Tarefas** > **Selecione grupos**.
   1. Adicione o grupo de segurança **Secure Workstations**.
   1. Selecione **Guardar**.

## <a name="enroll-and-validate-your-first-device"></a>Inscreva-se e valide o seu primeiro dispositivo

1. Para inscrever o seu dispositivo, necessita das seguintes informações:
   * **Número de série** - encontrado no chassis do dispositivo.
   * **ID do Produto Windows** - encontrado no **sistema** > **sobre** o menu definições do Windows.
   * Pode executar [o Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) para obter um ficheiro de haxixe CSV com todas as informações necessárias para a inscrição do dispositivo.
   
     Corra `Get-WindowsAutoPilotInfo – outputfile device1.csv` para obter a informação como um ficheiro CSV que pode importar para Intune.

     > [!NOTE]
     > O guião requer direitos elevados. Funciona como assinado à distância. O `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` comando permite que o script corra corretamente.

   * Pode recolher estas informações inserindo-se num dispositivo 1809 ou superior do Windows 10. O seu revendedor de hardware também pode fornecer esta informação.
1. No **portal Azure**, vá ao **Microsoft Intune** > **Device matricular** > dispositivos de**inscrição** > **- Gerir dispositivos de piloto automático do Windows**.
1. Selecione **Importar** e escolha o seu ficheiro CSV.
1. Adicione o dispositivo ao grupo de segurança **Secure Workstations.**
1. No dispositivo Windows 10 pretende configurar, vá ao **Windows Settings** > **Update &** > **Security Recovery**.
   1. Escolha **Começar** em **Reset este PC**.
   1. Siga as instruções para redefinir e reconfigurar o dispositivo com as políticas de perfil e conformidade configuradas.

Depois de configurar o dispositivo, complete uma revisão e verifique a configuração. Confirme que o primeiro dispositivo está configurado corretamente antes de continuar a sua implementação.

## <a name="assign-devices"></a>Atribua dispositivos

Para atribuir dispositivos e utilizadores, é necessário mapear os [perfis selecionados](/intune/device-profile-assign) para o seu grupo de segurança. Todos os novos utilizadores que necessitem de permissões ao serviço devem também ser adicionados ao grupo de segurança.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Utilização do Sentinel e do Windows Defender ATP para monitorizar e responder a incidentes de segurança

Monitorização da implantação segura da estação de trabalho pode ser realizada permitindo [Sentinel] e utilizando a Gestão de [Ameaças e Vulnerabilidades](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) A orientação não fornecerá uma caça exaustiva de ameaças, mas proporcionará esforços de bom senso para monitorizar e responder a potenciais incidentes de segurança.

Usaremos **o Azure Sentinel** para: 

* Recolher dados do Intune, portal Azure e Azure AD para monitorização de utilizadores e dispositivos
* Ajude a investigar a configuração do utilizador e do dispositivo atividade suspeita
* E impulsionar a capacidade de explorar investigações de segurança usando wdatp

A monitorização do Sentinel requer a criação de conectores às fontes de dados, como o Azure AD.

1. No **portal Azure,** vá ao **Azure Sentinel (Pré-visualização)** > Selecione **Adicionar**
1. No **Escolha um espaço de trabalho para adicionar ao Azure Sentinel** Select **Criar um novo espaço de trabalho**
1. Introduza:
   * **Log Analytics Workspace** - 'Monitorização segura da estação de trabalho'
   * **Subscrição** - Selecione a sua subscrição ativa
   * **Grupo de Recursos** - selecione o ** Create New** > Secure Workstation RG > **Ok**
   * **Localização** - Selecione a localização que é geograficamente mais adequada para a sua implementação
   * **Preço -** Selecione **por GB (2018)**
1. Selecione **Ok**.

Em seguida, ligaremos fontes de dados seguras disponíveis da estação de trabalho à monitorização.

1. No **portal Azure,** vá ao espaço de **trabalho Do Azure Sentinel** > Selecione espaço de trabalho de **monitorização de estações** de trabalho seguras
1. Selecione **Conectores de Dados**
1. Escolha o **Diretório Ativo Azure** > página de conector aberto > depois de rever o pré-requisito. Proceda à Configuração e selecione **Connect** para ambos os registos de acesso a AD Azure, bem como registos de auditoria azure AD.
1. Escolha a página de conector aberta > atividade **do Azure** > > depois de rever o pré-requisito. Proceda a Configure Registos de Atividade do Azure > Selecione a sua subscrição > Selecione **Connect**

À medida que os dados forem recolhidos pelo Sentinel poderá observar a atividade selecionando o **portal Azure,** vá ao **Azure Sentinel Overview** 

Utilizaremos o **Windows Defender ATP (WDATP)** para:

* Observe misonsidades e monitorização contínuas de vulnerabilidades e configurações erradas
* Utilize o WDATP para priorizar ameaças dinâmicas na natureza
* Correlação de unidade de vulnerabilidades com alertas de deteção e resposta de pontofinal (EDR)
* Utilize o painel de instrumentos para identificar a vulnerabilidade ao nível da máquina durante as investigações
* Empurre as reparações para Intune

Configure o seu [painel ATP Defender](https://securitycenter.windows.com/machines). Utilizando orientação na visão geral do painel de instrumentos de gestão de [vulnerabilidades de Ameaça &](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Monitorizar a atividade da aplicação utilizando o Microsoft Monitoring Agent (MMA)
A partir da estação de trabalho especializada, o cacifo de aplicações está habilitado para monitorizar a atividade de aplicação numa estação de trabalho. Para que a monitorização seja integrada no seu espaço de trabalho Log Analytics, deve ser seguido um agente e configuração de MMA. 

> [!NOTE]
> O perfil de estação de trabalho especializado contém as políticas de monitorização do AppLocker. A implementação das políticas é necessária para a monitorização da atividade de aplicação num cliente

Implementar o agente MMA com o script Intune PowerShell

1. Descarregue o script de configuração [para um dispositivo local](https://aka.ms/securedworkstationgit).
1. Atualize os parâmetros, **$WorkSpaceID** e **$WorkSpaceKey**
1. Navegue no **portal** > Azure**Microsoft Intune** > **Device configuração** > **PowerShell scripts** > **Add**.
1. Forneça um **nome** para o script e especifique a localização do **Script**.
1. Selecione **Configurar**.
   1. Definir **Executar este script utilizando as credenciais registadas** para **Sim**.
   1. Selecione **OK**.
1. Selecione **Criar**.
1. Selecione **Tarefas** > **Selecione grupos**.
   1. Adicione o grupo de segurança **Secure Workstations**.
   1. Selecione **Guardar**.

Em seguida, tem de configurar o Log Analytics para receber os novos registos
1. No **portal Azure,** vá ao **Log Analytics Workspace** > Select - 'Monitorização segura da estação de trabalho'
1. Selecione registos de eventos de data do Windows de **definições** > **Data** > **avançadas**
1. Em **Recolher eventos a partir dos seguintes registos de eventos** 
1. Introduza:
   * 'Microsoft-Windows-AppLocker/EXE e DLL' > Informação Não **Selectal**
   * 'Microsoft-Windows-AppLocker/MSI e Script' > **Informação** Não Selecionada
   * 'Microsoft-Windows-AppLocker/App-Deployment embalado' > **Informação** Não Selectal
   * 'Microsoft-Windows-AppLocker/App-Execution embalado' > **Informação** Não Selecionada
1. Selecione **Guardar**

O registo de aplicações estará disponível no espaço de trabalho selecionado para log analytics.

## <a name="monitoring"></a>Monitorização

* Saiba como [detetar ameaças com o Sentinela Azure](/azure/sentinel/tutorial-detect-threats)
* [Investigar incidentes com O Sentinela](/azure/sentinel/tutorial-investigate-cases)
* [Criar respostas de ameaça automatizadas no Azure Sentinel](/azure/sentinel/tutorial-respond-threats-playbook)
* Entenda como rever a sua [Pontuação de Exposição](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Recomendação [de segurança](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation) de revisão
* Gerir [remediações de](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) segurança
* Gerir a [deteção e resposta de pontofinal](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Monitorize os perfis com monitorização de [perfil Intune](/intune/device-profile-monitor).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Microsoft Intune](/intune/index).
* Compreenda [a AD Azure.](../index.yml)
* Trabalhar com a [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Descubra [o Sentinela Azure](/azure/sentinel/)
