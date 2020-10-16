---
title: Resolução de problemas Enterprise State Roaming in Azure Ative Directory
description: Fornece respostas a algumas perguntas que os administradores de TI podem ter sobre configurações e sincronização de dados de aplicações.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: e427a68751cc348a7b0024fb01402bd15ca87d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705729"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Resolução de problemas das configurações do roaming do estado da empresa no Diretório Ativo do Azure

Este tópico fornece informações sobre como resolver problemas e diagnosticar problemas com o Roaming do Estado da Empresa, e fornece uma lista de questões conhecidas.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Este artigo aplica-se ao navegador baseado em HTML do Microsoft Edge Legacy lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Chromium microsoft Edge lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="preliminary-steps-for-troubleshooting"></a>Passos preliminares para a resolução de problemas 

Antes de iniciar a resolução de problemas, verifique se o utilizador e o dispositivo foram configurados corretamente e que todos os requisitos do Roaming do Estado da Empresa são cumpridos pelo dispositivo e pelo utilizador. 

1. O Windows 10, com as mais recentes atualizações, e uma versão mínima 1511 (OS Build 10586 ou posterior) encontra-se instalado no dispositivo. 
1. O dispositivo é a Azure AD aderido ou híbrido Azure AD juntou-se. Para obter mais informações, [consulte como obter um dispositivo sob o controlo da Azure AD](overview.md).
1. Certifique-se de que o **Roaming do Estado da Empresa** está habilitado para o inquilino em Azure AD, conforme descrito em Enable Enterprise State [Roaming](enterprise-state-roaming-enable.md). Pode ativar o roaming para todos os utilizadores ou apenas para um grupo selecionado de utilizadores.
1. Ao utilizador é atribuída uma licença Azure Ative Directory Premium.  
1. O dispositivo tem de ser reiniciado e o utilizador deve iniciar sedução novamente para aceder às funcionalidades de Roaming do Estado da Empresa.

## <a name="information-to-include-when-you-need-help"></a>Information to include when you need help (Informações a incluir se precisar de ajuda)
Se não conseguir resolver o seu problema com a orientação abaixo, pode contactar os nossos engenheiros de suporte. Quando os contactar, inclua as seguintes informações:

* **Descrição geral do erro**: Existem mensagens de erro vistas pelo utilizador? Se não houve mensagem de erro, descreva o comportamento inesperado que notou, em detalhe. Que funcionalidades estão ativadas para sincronização e o que espera o utilizador sincronizar? Várias funcionalidades não estão a sincronizar ou estão isoladas a uma?
* **Utilizadores afetados** – A sincronização está a funcionar/a falhar para um utilizador ou vários utilizadores? Quantos dispositivos estão envolvidos por utilizador? Não estão todos a sincronizar ou alguns deles estão a sincronizar e alguns não estão a sincronizar?
* **Informação sobre o utilizador** – Que identidade utiliza o utilizador para iniciar sôm no dispositivo? Como é que o utilizador está a iniciar sessão no dispositivo? Fazem parte de um grupo de segurança selecionado que pode sincronizar? 
* **Informação sobre o dispositivo** – Este dispositivo Azure AD-ad-joined ou domain-joined? Em que construção está o dispositivo? Quais são as atualizações mais recentes?
* **Data / Hora / Timezone** – Qual foi a data e hora exatas em que viu o erro (incluir o timezone)?

Incluir esta informação ajuda-nos a resolver o seu problema o mais rapidamente possível.

## <a name="troubleshooting-and-diagnosing-issues"></a>Resolução de problemas e diagnosticar problemas

Esta secção dá sugestões sobre como resolver problemas e diagnosticar problemas relacionados com o Roaming do Estado da Empresa.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Verifique a sincronização e a página de definições "Sincronizar as definições" 

1. Depois de juntar o seu PC do Windows 10 a um domínio configurado para permitir o Roaming do Estado da Empresa, inscreva-se na sua conta de trabalho. Vá a **Definições**  >  **Accounts**  >  **Sync As Suas Definições** e confirme que a sincronização e as definições individuais estão acesas, e que o topo da página de definições indica que está a sincronizar com a sua conta de trabalho. Confirme que a mesma conta também é utilizada como conta de login em **Definições**  >  **Contas**  >  **O Seu Info**. 
1. Verifique se a sincronização funciona em várias máquinas, fazendo algumas alterações na máquina original, tais como mover a barra de tarefas para o lado direito ou superior do ecrã. Observe a mudança propagar-se para a segunda máquina dentro de cinco minutos. 

   * Bloquear e desbloquear o ecrã (Win + L) pode ajudar a desencadear uma sincronização.
   * Deve estar a iniciar sessão com a mesma conta em ambos os PCs para sincronização para funcionar – uma vez que o Roaming do Estado da Empresa está ligado à conta de utilizador e não à conta da máquina.

**Problema potencial**: Se os controlos na página **Definições** não estiverem disponíveis e vir a mensagem "Algumas funcionalidades do Windows só estão disponíveis se estiver a utilizar uma conta microsoft ou uma conta de trabalho." Este problema pode surgir para dispositivos configurados para serem unidos e registados no Azure AD, mas o dispositivo ainda não foi autenticado com sucesso para a Azure AD. Uma causa possível é que a política do dispositivo deve ser aplicada, mas esta aplicação acontece de forma assíncronea, podendo ser adiada por algumas horas. 

### <a name="verify-the-device-registration-status"></a>Verifique o estado de registo do dispositivo

O Roaming do Estado da Empresa exige que o dispositivo seja registado no Azure AD. Embora não seja específico para o Roaming do Estado da Empresa, seguir as instruções abaixo pode ajudar a confirmar que o Cliente Windows 10 está registado, e confirmar a impressão digital, as definições de AD AD, o estado de NGC e outras informações.

1. Abra o pedido de comando sem relevância. Para isso no Windows, abra o lançador Run (Win + R) e escreva "cmd" para abrir.
1. Assim que o pedido de comando estiver aberto, escreva "*dsregcmd.exe /status*".
1. Para a saída esperada, o valor de campo **AzureAdJoined** deve ser "SIM", o valor de campo **WamDefaultSet** deve ser "SIM", e o valor de campo **WamDefaultGUID** deve ser um GUID com "(AzureAd)" no final.

**Problema potencial**: **WamDefaultSet** e **AzureAdJoined** ambos têm "NO" no valor de campo, o dispositivo foi ligado ao domínio e registado no Azure AD, e o dispositivo não sincroniza. Se estiver a mostrar isto, o dispositivo poderá ter de esperar pela aplicação da política ou a autenticação do dispositivo falhou ao ligar-se ao Azure AD. O utilizador poderá ter de esperar algumas horas para que a apólice seja aplicada. Outras etapas de resolução de problemas podem incluir a refilmagem automática, assinando e voltando a entrar, ou lançando a tarefa no Task Scheduler. Em alguns casos, executar "*dsregcmd.exe /leave*" em uma janela de aviso de comando elevado, reiniciar e tentar o registo novamente pode ajudar com este problema.

**Problema potencial**: O campo de **DefiniçõesUrl** está vazio e o dispositivo não sincroniza. O utilizador pode ter iniciado o seu login no dispositivo antes de o Roaming do Estado da Empresa ter sido ativado no Portal do Diretório Ativo do Azure. Reinicie o dispositivo e faça o login do utilizador. Opcionalmente, no portal, tente que o Administrador de TI navegue para **a Azure Ative Directory**  >  **Devices**  >  **Enterprise State Roaming** e reativar os **Utilizadores podem sincronizar configurações e dados de aplicações através de dispositivos**. Uma vez re-activado, reinicie o dispositivo e faça o login do utilizador. Se isto não resolver o problema, **o SettingsUrl** pode estar vazio se houver um certificado de dispositivo estragado. Neste caso, executar "*dsregcmd.exe /leave*" numa janela de aviso de comando elevado, reiniciar e tentar o registo novamente pode ajudar neste problema.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming do Estado da Empresa e Autenticação multi-factor 

Sob certas condições, o Roaming do Estado da Empresa pode não sincronizar dados se a autenticação multi-factor Azure estiver configurada. Para obter mais informações sobre estes sintomas, consulte o documento de suporte [KB3193683](https://support.microsoft.com/kb/3193683). 

**Problema potencial**: Se o seu dispositivo estiver configurado para necessitar de autenticação multi-factor no portal Azure Ative Directory, poderá falhar em sincronizar as definições enquanto iniciar a sessão num dispositivo Windows 10 utilizando uma palavra-passe. Este tipo de configuração de autenticação multi-factor destina-se a proteger uma conta de administrador Azure. Os utilizadores de administração podem ainda ser capazes de sincronizar ao iniciar sessão nos seus dispositivos Windows 10 com o microsoft Passport for Work PIN ou completando a autenticação multi-factor enquanto acedem a outros serviços Azure como o Microsoft 365.

**Problema potencial**: O Sync pode falhar se o administrador configurar a política de acesso condicional de autenticação de múltiplos fatores do Diretório Ativo e o token de acesso no dispositivo expirar. Certifique-se de que faz sedida e assina usando o Microsoft Passport for Work PIN ou complete a Autenticação Multi-Factor ao aceder a outros serviços Azure como o Microsoft 365.

### <a name="event-viewer"></a>Visualizador de Eventos

Para uma resolução avançada de problemas, o Event Viewer pode ser usado para encontrar erros específicos. Estes estão documentados na tabela abaixo. Os eventos podem ser encontrados no caso do Espectador > **aplicações e serviços**  >  **Microsoft**  >  **Windows**  >  **SettingSync-Azure** e para problemas relacionados com identidade com aplicações de sincronização e **registos de serviços**  >  **Microsoft**  >  **Windows**  >  **AAD**.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>O Sync não funciona em dispositivos que tenham apps carregadas lateralmente usando software MDM

Afeta os dispositivos que executam a atualização de aniversário do Windows 10 (versão 1607). No Espectador de Eventos sob os registos SettingSync-Azure, o ID do evento 6013 com erro 80070259 é frequentemente visto.

**Ação recomendada**  
Certifique-se de que o cliente windows 10 v1607 tem a Atualização Cumulativa de 23 de agosto de 2016[(KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Os favoritos do Internet Explorer não sincronizam

Afeta os dispositivos que executam a atualização do Windows 10 de novembro (versão 1511).

**Ação recomendada**  
Certifique-se de que o cliente windows 10 v1511 tem a Atualização Cumulativa de julho de 2016[(KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>O tema não é sincronização, assim como dados protegidos com a Proteção de Informação do Windows 

Para evitar fugas de dados, os dados protegidos com [a Proteção de Informações](/windows/security/information-protection/windows-information-protection/protect-enterprise-data-using-wip) do Windows não sincronizarão através do Roaming do Estado da Empresa para dispositivos que utilizem a Atualização de Aniversário do Windows 10.

**Ação recomendada**  
Nenhum. Futuras atualizações para o Windows podem resolver este problema.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>As definições de data, hora e região não sincronizam no dispositivo ligado ao domínio 
  
Os dispositivos que se unem ao domínio não experimentarão sincronização para a definição Data, Hora e Região: tempo automático. A utilização do tempo automático pode anular as definições de data, hora e região e fazer com que essas definições não se sincronizem. 

**Ação recomendada**  
Nenhum. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC Solicita ao sincronizar palavras-passe

Afeta os dispositivos que executam a atualização do Windows 10 de novembro (versão 1511) com um NIC sem fios configurado para sincronizar palavras-passe.

**Ação recomendada**  
Certifique-se de que o cliente Windows 10 v1511 tem a Atualização Cumulativa[(KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>O Sync não funciona em dispositivos que usam cartão inteligente para login

Se tentar iniciar sposição no seu dispositivo Windows utilizando um cartão inteligente ou um cartão inteligente virtual, as definições sincronizadas deixarão de funcionar.     

**Ação recomendada**  
Nenhum. Futuras atualizações para o Windows podem resolver este problema.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Dispositivo de ligação ao domínio não está a sincronizar depois de deixar a rede corporativa     

Os dispositivos associados ao domínio registados no Azure AD podem sofrer uma falha de sincronização se o dispositivo estiver fora do local por longos períodos de tempo, e a autenticação de domínio não puder ser concluída.

**Ação recomendada**  
Ligue o dispositivo a uma rede corporativa para que a sincronização possa ser retomada.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>O dispositivo Azure AD Joined não está a sincronizar e o utilizador tem um nome principal de utilizador de caso misto.

Se o utilizador tiver uma UPN de caixa mista (por exemplo, Nome do Utilizador em vez de nome de utilizador) e o utilizador estiver num dispositivo AZure AD Joined, que foi atualizado do Windows 10 Build 10586 para 14393, o dispositivo do utilizador pode não sincronizar. 

**Ação recomendada**  
O utilizador terá de se juntar e voltar a juntar o dispositivo à nuvem. Para isso, inicie sessão como utilizador do Administrador Local e desacose o dispositivo indo para o Sistema **de Definições**  >  **System**  >  **Sobre** e selecione "Gerir ou desligar do trabalho ou da escola". Limpe os ficheiros abaixo e, em seguida, Azure AD Junte o dispositivo novamente no Sistema **de Definições**  >  **System**  >  **Sobre** e selecione "Connect to Work or School". Continue a juntar o dispositivo ao Azure Ative Directory e complete o fluxo.

Na etapa de limpeza, limpe os seguintes ficheiros:
- Configurações.dat in `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Todos os ficheiros sob a pasta `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID do evento 6065: 80070533 Este utilizador não pode iniciar sação porque esta conta está atualmente desativada  

No Visualizador de Eventos sob os registos SettingSync/Debug, este erro pode ser visto quando as credenciais do utilizador expirarem. Além disso, pode ocorrer quando o arrendatário não tinha automaticamente a AzureRMS a provisionada. 

**Ação recomendada**  
No primeiro caso, tenha o utilizador a atualizar as suas credenciais e faça login no dispositivo com as novas credenciais. Para resolver a questão do AzureRMS, proceda aos passos enumerados no [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>ID do evento 1098: Erro: 0xCAA5001C Operação de corretor de token falhou  

No Visualizador de Eventos sob os registos AAD/Operacional, este erro pode ser visto com o Evento 1104: AAD Cloud AP plugin call Get token return error: 0xC00005F. Este problema ocorre se houver permissões em falta ou atributos de propriedade.  

**Ação recomendada**  
Prossiga com os passos listados [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral, consulte [a visão geral do roaming do estado da empresa.](enterprise-state-roaming-overview.md)