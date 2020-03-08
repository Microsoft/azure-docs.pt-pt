---
title: Troubleshoot Enterprise State Roaming em Diretório Ativo Azure
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
ms.openlocfilehash: ae8ce24aeb665a7f99326e83dbe18d020e1b6196
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672355"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Resolução de problemas configurações de roaming do Estado da Empresa em Diretório Ativo Azure

Este tópico fornece informações sobre como resolver problemas e diagnosticar problemas com o Roaming do Estado da Empresa, e fornece uma lista de questões conhecidas.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Este artigo aplica-se ao navegador baseado no Microsoft Edge Legacy HTML lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Crómio microsoft Edge, lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="preliminary-steps-for-troubleshooting"></a>Passos preliminares para resolução de problemas 

Antes de iniciar a resolução de problemas, verifique se o utilizador e o dispositivo foram configurados corretamente e que todos os requisitos do Enterprise State Roaming são cumpridos pelo dispositivo e pelo utilizador. 

1. O Windows 10, com as mais recentes atualizações, e uma versão mínima 1511 (OS Build 10586 ou posterior) está instalada no dispositivo. 
1. O dispositivo é Azure AD aderiu ou aD Azure híbrido juntou-se. Para mais informações, consulte [como obter um dispositivo sob o controlo da AD Azure](overview.md).
1. Certifique-se de que o **roaming do Estado Empresarial** está habilitado para o inquilino em Azure AD, conforme descrito em permitir o roaming do Estado Da [Empresa](enterprise-state-roaming-enable.md). Pode ativar o roaming para todos os utilizadores ou para apenas um grupo selecionado de utilizadores.
1. Foi atribuído ao utilizador uma licença Azure Ative Directory Premium.  
1. O dispositivo deve ser reiniciado e o utilizador deve voltar a entrar para aceder às funcionalidades de Roaming do Estado da Empresa.

## <a name="information-to-include-when-you-need-help"></a>Informações para incluir quando precisa de ajuda
Se não conseguir resolver o seu problema com as orientações abaixo, pode contactar os nossos engenheiros de suporte. Quando os contactar, inclua as seguintes informações:

* **Descrição geral do erro**: Existem mensagens de erro vistas pelo utilizador? Se não houve uma mensagem de erro, descreva o comportamento inesperado que notou, em detalhe. Quais as funcionalidades ativadas para sincronização e o que espera o utilizador sincronizar? As múltiplas funcionalidades não estão a sincronizar ou está isolada a uma?
* **Utilizadores afetados** – O sincronização está a funcionar/a falhar para um utilizador ou vários utilizadores? Quantos dispositivos estão envolvidos por utilizador? Todos eles não estão sincronizados ou alguns deles estão a sincronizar-se e alguns não estão a sincronizar?
* **Informação sobre o utilizador** – Que identidade está o utilizador a usar para iniciar sessão no dispositivo? Como é que o utilizador está a fazer sessão no dispositivo? Eles fazem parte de um grupo de segurança selecionado para sincronizar? 
* **Informações sobre o dispositivo** – Este dispositivo é aderido a AD azure ou unificado por domínio? Em que construção está o dispositivo? Quais são as atualizações mais recentes?
* **Data / Hora / Fuso Horário** – Qual foi a data e hora exatas em que viu o erro (incluir o fuso horário)?

Incluir esta informação ajuda-nos a resolver o seu problema o mais rapidamente possível.

## <a name="troubleshooting-and-diagnosing-issues"></a>Resolução de problemas e diagnosticar problemas

Esta secção dá sugestões sobre como resolver problemas e diagnosticar problemas relacionados com o Roaming do Estado Da Empresa.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Verifique a sincronização e a página de definições "Sync your Settings" 

1. Depois de juntar o seu PC Windows 10 a um domínio configurado para permitir o Roaming do Estado Da Empresa, inscreva-se na sua conta de trabalho. Vá a **Definições** > **Contas** > Sincronizar as **definições** e confirmar que o sincronização e as definições individuais estão ligados, e que o topo da página de definições indica que está a sincronizar com a sua conta de trabalho. Confirme que a mesma conta também é utilizada como conta de login em **Definições** > **Contas** > **Sua Informação**. 
1. Verifique se a sincronização funciona através de várias máquinas fazendo algumas alterações na máquina original, tais como mover a barra de tarefas para o lado direito ou superior do ecrã. Observe a mudança propagar para a segunda máquina dentro de cinco minutos. 

   * Bloquear e desbloquear o ecrã (Win + L) pode ajudar a desencadear uma sincronização.
   * Deve estar a iniciar sessão com a mesma conta em ambos os PCs para sincronizar o funcionar – uma vez que o Roaming do Estado Da Empresa está ligado à conta de utilizador e não à conta da máquina.

**Problema potencial**: Se os controlos na página **Definições** não estiverem disponíveis e vir a mensagem "Algumas funcionalidades do Windows só estão disponíveis se estiver a utilizar uma conta microsoft ou uma conta de trabalho." Este problema pode surgir para dispositivos que estão configurados para serem unidos e registados em Azure AD, mas o dispositivo ainda não foi autenticado com sucesso à Azure AD. Uma possível causa é que a política do dispositivo deve ser aplicada, mas esta aplicação acontece de forma assíncrona, podendo ser adiada por algumas horas. 

### <a name="verify-the-device-registration-status"></a>Verifique o estado de registo do dispositivo

Enterprise State Roaming requer que o dispositivo seja registado na Azure AD. Embora não seja específico do Roaming do Estado Da Empresa, seguindo as instruções abaixo pode ajudar a confirmar que o Cliente Windows 10 está registado, e confirmar a impressão digital, url de definições de AD Azure, estado NGC e outras informações.

1. Abra o pedido de comando sem elevação. Para isso no Windows, abra o lançador Run (Win + R) e escreva "cmd" para abrir.
1. Uma vez aberta a solicitação de comando, escreva "*dsregcmd.exe /status*".
1. Para uma saída esperada, o valor de campo **AzureAd Joind** deve ser "SIM", o valor do campo **WamDefaultSet** deve ser "SIM", e o valor de campo **WamDefaultGUID** deve ser um GUID com "(AzureAd)" no final.

**Problema potencial**: **WamDefaultSet** e **AzureAdJoined** ambos têm "NO" no valor de campo, o dispositivo foi unido e registado com AD Azure, e o dispositivo não sincroniza. Se o apresentar, o dispositivo poderá ter de esperar pela aplicação da política ou a autenticação do dispositivo falhou ao ligar-se ao Azure AD. O utilizador pode ter de esperar algumas horas para que a apólice seja aplicada. Outras etapas de resolução de problemas podem incluir a reexperimentação do registo automático, assinando e voltando a entrar, ou lançando a tarefa no Agendador de Tarefas. Em alguns casos, correr "*dsregcmd.exe /leave*" em uma janela de comando elevado, reiniciar e tentar registrar novamente pode ajudar com este problema.

**Problema potencial**: O campo para **DefiniçõesUrl** está vazio e o dispositivo não sincroniza. O utilizador pode ter entrado pela última vez no dispositivo antes de o Enterprise State Roaming ter sido ativado no Portal do Diretório Ativo Azure. Reinicie o dispositivo e faça o login do utilizador. Opcionalmente, no portal, tente que o Administrador de TI navegue para **o Azure Ative Directory** > **Dispositivos** > **Desativação** do Roaming do Estado Empresarial e reativar **os Utilizadores podem sincronizar definições e dados de aplicações através de dispositivos.** Uma vez reativado, reinicie o dispositivo e faça o login do utilizador. Se isto não resolver o problema, **o DefiniçõesUrl** pode ficar vazio se houver um mau certificado de dispositivo. Neste caso, executar "*dsregcmd.exe /leave*" numa janela de comando elevado, reiniciar e tentar registo novamente pode ajudar nesta questão.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming do Estado Empresarial e Autenticação multi-factor 

Em determinadas condições, o Roaming do Estado da Empresa pode não sincronizar dados se a Autenticação de Multifactor azure estiver configurada. Para obter mais informações sobre estes sintomas, consulte o documento de suporte [KB3193683](https://support.microsoft.com/kb/3193683). 

**Problema potencial**: Se o seu dispositivo estiver configurado para exigir a autenticação multi-factor no portal do Diretório Ativo Azure, poderá não sincronizar as definições ao iniciar sessão num dispositivo Windows 10 utilizando uma palavra-passe. Este tipo de configuração de autenticação multi-factor destina-se a proteger uma conta de administrador Azure. Os utilizadores de administração podem ainda ser capazes de sincronizar ao iniciar sessão nos seus dispositivos Windows 10 com o seu Microsoft Passport for Work PIN ou através do preenchimento da Autenticação Multi-Factor enquanto acedem a outros serviços do Azure, como o Office 365.

**Problema potencial**: O Sync pode falhar se o administrador configurar a política de acesso condicional de autenticação multi-factor da Federação de Diretórios Ativos e o token de acesso no dispositivo expirar. Certifique-se de que inscreveu e assina utilizando o Microsoft Passport para O PIN de Trabalho ou complete a Autenticação Multi-Factor ao aceder a outros serviços do Azure, como o Office 365.

### <a name="event-viewer"></a>Espectador de Eventos

Para resolução avançada de problemas, o Espectador de Eventos pode ser usado para encontrar erros específicos. Estes estão documentados na tabela abaixo. Os eventos podem ser encontrados no Event Viewer > Applications and Services Logs > **Microsoft** > **Windows** > **DefiniçãoSync-Azure** e para problemas relacionados com a identidade com a Sincronização **microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Sync não funciona em dispositivos que têm apps de lado usando software MDM

Afeta os dispositivos que executam a Atualização de Aniversário do Windows 10 (versão 1607). No Visualizador de Eventos sob os registos SettingSync-Azure, o Id do evento 6013 com erro 80070259 é frequentemente visto.

**Ação recomendada**  
Certifique-se de que o cliente Windows 10 v1607 tem a Atualização Cumulativa de 23 de agosto de 2016 ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Os favoritos do Internet Explorer não sincronizam

Afeta os dispositivos que executam a Atualização do Windows 10 de novembro (versão 1511).

**Ação recomendada**  
Certifique-se de que o cliente Windows 10 v1511 tem a Atualização Cumulativa de julho de 2016 ([KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>O tema não está a sincronizar, bem como os dados protegidos com a Proteção de Informação do Windows 

Para evitar fugas de dados, os dados protegidos com a [Proteção de Informação do Windows](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) não sincronizarão através do Roaming do Estado da Empresa para dispositivos que utilizem a Atualização de Aniversário do Windows 10.

**Ação recomendada**  
Nenhum. Futuras atualizações para o Windows podem resolver este problema.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Definições de data, hora e região não sincronizam no dispositivo unido ao domínio 
  
Os dispositivos que são unidos pelo domínio não irão sentir sincronização para a definição Data, Hora e Região: tempo automático. A utilização automática do tempo pode anular as definições de data, hora e região e fazer com que essas definições não se sincroniem. 

**Ação recomendada**  
Nenhum. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC Solicita ao sincronizar palavras-passe

Afeta dispositivos que executam a Atualização do Windows 10 de novembro (Versão 1511) com um NIC sem fios que está configurado para sincronizar palavras-passe.

**Ação recomendada**  
Certifique-se de que o cliente Do Windows 10 v1511 tem a Atualização Cumulativa[(KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Sync não funciona em dispositivos que usam cartão inteligente para login

Se tentar iniciar sessão no seu dispositivo Windows utilizando um cartão inteligente ou um cartão inteligente virtual, a sincronização de definições deixará de funcionar.     

**Ação recomendada**  
Nenhum. Futuras atualizações para o Windows podem resolver este problema.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>O dispositivo de domínio não está a sincronizar-se depois de deixar a rede corporativa     

Os dispositivos unidos pelo domínio registados em Azure AD podem sofrer uma falha de sincronização se o dispositivo estiver fora do local por longos períodos de tempo, e a autenticação de domínio não puder ser concluída.

**Ação recomendada**  
Ligue o dispositivo a uma rede corporativa para que a sincronização possa ser retomada.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>O dispositivo Azure AD Joined não está a sincronizar e o utilizador tem um nome principal de utilizador misto.

Se o utilizador tiver um caso misto UPN (por exemplo, UserName em vez de username) e o utilizador estiver num dispositivo Azure AD Joined, que atualizou do Windows 10 Build 10586 para 14393, o dispositivo do utilizador pode não conseguir sincronizar. 

**Ação recomendada**  
O utilizador terá de se desjuntar e voltar a juntar o dispositivo à nuvem. Para isso, inicie o login como utilizador do Administrador Local e desintegre o dispositivo indo para **Definições** > **Sistema** > **Sobre** e selecione "Gerir ou desligar do trabalho ou da escola". Limpe os ficheiros abaixo e, em seguida, Azure AD Junte-se ao dispositivo novamente em **Definições** > **Sistema** > **Sobre** e selecione "Connect to Work or School". Continue a juntar o dispositivo ao Azure Ative Directory e complete o fluxo.

Na etapa de limpeza, limpe os seguintes ficheiros:
- Definições.dat em `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Todos os ficheiros sob a pasta `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID do evento 6065: 80070533 Este utilizador não pode iniciar sessão porque esta conta está atualmente desativada  

No Evento Viewer sob os registos DefiniçãoSync/Debug, este erro pode ser visto quando as credenciais do utilizador expirarem. Além disso, pode ocorrer quando o inquilino não tinha automaticamente azureRMS provisionado. 

**Ação recomendada**  
No primeiro caso, faça com que o utilizador atualize as suas credenciais e faça login no dispositivo com as novas credenciais. Para resolver a questão do AzureRMS, proceda com as etapas enumeradas no [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Id do evento 1098: Erro: operação de corretor de token 0xCAA5001C falhou  

Em Evento Espectador sob os registos AAD/Operacional, este erro pode ser visto com o Evento 1104: AAD Cloud AP plugin call Get token returned error: 0xC0000005F. Este problema ocorre se faltarperes ou atributos de propriedade.  

**Ação recomendada**  
Prossiga com as etapas listadas [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral, consulte a [visão geral do estado empresarial.](enterprise-state-roaming-overview.md)
