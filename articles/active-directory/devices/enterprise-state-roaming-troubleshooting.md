---
title: Solucionar problemas de Enterprise State Roaming no Azure Active Directory
description: Fornece respostas para algumas perguntas que os administradores de ti podem ter sobre configurações e sincronização de dados de aplicativo.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad897ea73f32327b894558c5c04449c667663dad
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379752"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Solucionando problemas de configurações de Enterprise State Roaming no Azure Active Directory

Este tópico fornece informações sobre como solucionar e diagnosticar problemas com o Enterprise State Roaming e fornece uma lista de problemas conhecidos.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="preliminary-steps-for-troubleshooting"></a>Etapas preliminares para solução de problemas 

Antes de iniciar a solução de problemas, verifique se o usuário e o dispositivo foram configurados corretamente e se todos os requisitos de Enterprise State Roaming são atendidos pelo dispositivo e pelo usuário. 

1. O Windows 10, com as atualizações mais recentes, e uma versão mínima de 1511 (so Build 10586 ou posterior) é instalado no dispositivo. 
1. O dispositivo é associado ao Azure AD ou ao Azure AD híbrido. Para obter mais informações, consulte [como obter um dispositivo sob controle do Azure ad](overview.md).
1. Verifique se **Enterprise State roaming** está habilitado para o locatário no Azure AD, conforme descrito em [para habilitar o Enterprise State roaming](enterprise-state-roaming-enable.md). Você pode habilitar o roaming para todos os usuários ou apenas para um grupo de usuários selecionado.
1. O usuário já deve ter uma licença de Azure Active Directory Premium atribuída.  
1. O dispositivo deve ser reiniciado e o usuário deve entrar novamente para acessar os recursos do Enterprise State Roaming.

## <a name="information-to-include-when-you-need-help"></a>Informações a serem incluídas quando precisar de ajuda
Se não for possível resolver o problema com as diretrizes abaixo, você poderá entrar em contato com nossos engenheiros de suporte. Ao contatá-los, inclua as seguintes informações:

* **Descrição geral do erro**: há mensagens de erro vistas pelo usuário? Se não houvesse nenhuma mensagem de erro, descreva o comportamento inesperado que você observou, em detalhes. Quais recursos estão habilitados para sincronização e o que o usuário espera sincronizar? Vários recursos não estão sendo sincronizados ou são isolados um?
* **Usuários afetados** – a sincronização está funcionando/falhando para um usuário ou vários usuários? Quantos dispositivos estão envolvidos por usuário? Todos eles não estão sincronizando ou estão sendo sincronizados e alguns não estão sincronizando?
* **Informações sobre o usuário** – Qual identidade o usuário está usando para entrar no dispositivo? Como o usuário está entrando no dispositivo? Eles fazem parte de um grupo de segurança selecionado que podem ser sincronizados? 
* **Informações sobre o dispositivo** – este dispositivo ingressou no Azure ad ou ingressado no domínio? Em que compilação o dispositivo está? Quais são as atualizações mais recentes?
* **Data/Hora/fuso horário** – Qual foi a data e hora exatas em que você viu o erro (inclua o fuso horário)?

A inclusão dessas informações nos ajuda a resolver seu problema o mais rápido possível.

## <a name="troubleshooting-and-diagnosing-issues"></a>Resolução de problemas e diagnosticar problemas
Esta seção fornece sugestões sobre como solucionar problemas relacionados a Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Verifique a sincronização e a página de configurações "sincronizar suas configurações" 

1. Depois de ingressar seu PC com Windows 10 em um domínio configurado para permitir Enterprise State Roaming, entre com sua conta corporativa. Acesse **configurações** > **contas** > **sincronizar suas configurações** e confirme se a sincronização e as configurações individuais estão ativadas e se a parte superior da página Configurações indica que você está sincronizando com sua conta corporativa. Confirme se a mesma conta também é usada como sua conta de logon em **configurações** > **contas** > **suas informações**. 
1. Verifique se a sincronização funciona em vários computadores fazendo algumas alterações no computador original, como mover a barra de tarefas para o lado direito ou superior da tela. Observe que a alteração é propagada para a segunda máquina dentro de cinco minutos. 

   * Bloquear e desbloquear a tela (Win + L) pode ajudar a disparar uma sincronização.
   * Você deve estar entrando com a mesma conta em ambos os computadores para que a sincronização funcione, pois Enterprise State Roaming está vinculado à conta de usuário e não à conta do computador.

**Possível problema**: se os controles na página **configurações** não estiverem disponíveis e você vir a mensagem "alguns recursos do Windows só estarão disponíveis se você estiver usando uma conta de trabalho ou conta Microsoft." Esse problema pode surgir em dispositivos que estão configurados para serem ingressados no domínio e registrados no Azure AD, mas o dispositivo ainda não foi autenticado com êxito para o Azure AD. Uma possível causa é que a política do dispositivo deve ser aplicada, mas esse aplicativo ocorre de forma assíncrona e pode ser atrasado em algumas horas. 

### <a name="verify-the-device-registration-status"></a>Verificar o status de registro do dispositivo

Enterprise State Roaming requer que o dispositivo seja registrado no Azure AD. Embora não seja específico para Enterprise State Roaming, seguir as instruções abaixo pode ajudar a confirmar se o cliente do Windows 10 está registrado e confirmar a impressão digital, a URL das configurações do Azure AD, o status do NGC e outras informações.

1. Abra o prompt de comando com privilégios elevados. Para fazer isso no Windows, abra o inicializador de execução (Win + R) e digite "cmd" para abrir.
1. Quando o prompt de comando estiver aberto, digite "*dsregcmd. exe/status*".
1. Para a saída esperada, o valor do campo **AzureAdJoined** deve ser "Yes", o valor do campo **WamDefaultSet** deve ser "Yes" e o valor do campo **WamDefaultGUID** deve ser um GUID com "(AzureAd)" no final.

**Possível problema**: **WamDefaultSet** e **AzureAdJoined** têm "no" no valor do campo, o dispositivo foi ingressado no domínio e registrado no Azure AD, e o dispositivo não é sincronizado. Se ele estiver mostrando isso, talvez o dispositivo precise aguardar a aplicação da política ou a autenticação do dispositivo falhou ao se conectar ao Azure AD. O usuário pode precisar aguardar algumas horas para que a política seja aplicada. Outras etapas de solução de problemas podem incluir a repetição do registro automático ao sair e voltar a entrar, ou iniciar a tarefa no Agendador de Tarefas. Em alguns casos, executar "*dsregcmd. exe/Leave*" em uma janela de prompt de comandos com privilégios elevados, reinicializar e tentar o registro novamente pode ajudar com esse problema.

**Possível problema**: o campo para **SettingsUrl** está vazio e o dispositivo não é sincronizado. O usuário pode ter feito o último logon no dispositivo antes de o Enterprise State Roaming ter sido habilitado no portal de Azure Active Directory. Reinicie o dispositivo e faça logon do usuário. Opcionalmente, no portal, tente fazer com que o administrador de ti navegue para **Azure Active Directory** **dispositivos** >  > **Enterprise State roaming** desabilitar e reabilitar **os usuários podem sincronizar configurações e dados de aplicativos entre dispositivos**. Depois de reabilitado, reinicie o dispositivo e faça logon do usuário. Se isso não resolver o problema, **SettingsUrl** poderá estar vazio no caso de um certificado de dispositivo inválido. Nesse caso, executar "*dsregcmd. exe/Leave*" em uma janela de prompt de comandos com privilégios elevados, reinicializar e tentar o registro novamente pode ajudar com esse problema.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming e autenticação multifator 

Em determinadas condições, Enterprise State Roaming pode falhar ao sincronizar dados se a autenticação multifator do Azure estiver configurada. Para obter detalhes adicionais sobre esses sintomas, consulte o documento de suporte [KB3193683](https://support.microsoft.com/kb/3193683). 

**Possível problema**: se o dispositivo estiver configurado para exigir autenticação multifator no portal de Azure Active Directory, você poderá não sincronizar as configurações ao entrar em um dispositivo Windows 10 usando uma senha. Esse tipo de configuração de autenticação multifator destina-se a proteger uma conta de administrador do Azure. Os usuários administradores ainda podem ser capazes de sincronizar entrando em seus dispositivos Windows 10 com seu Microsoft Passport for Work PIN ou concluindo a autenticação multifator ao acessar outros serviços do Azure, como o Office 365.

**Possível problema**: a sincronização poderá falhar se o administrador configurar a política de acesso condicional da autenticação multifator serviços de Federação do Active Directory (AD FS) e o token de acesso no dispositivo expirar. Certifique-se de entrar e sair usando o Microsoft Passport for Work PIN ou concluir a autenticação multifator ao acessar outros serviços do Azure, como o Office 365.

### <a name="event-viewer"></a>Visualizador de Eventos

Para solução de problemas avançada, Visualizador de Eventos pode ser usado para encontrar erros específicos. Eles estão documentados na tabela a seguir. Os eventos podem ser encontrados em Visualizador de Eventos > logs de aplicativos e serviços > **microsoft** > **Windows** > **SettingSync-Azure** e problemas relacionados à identidade com a sincronização **do Microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>A sincronização não funciona em dispositivos que têm aplicativos carregados pelo lado usando o software de MDM

Afeta os dispositivos que executam a atualização de aniversário do Windows 10 (versão 1607). Em Visualizador de Eventos nos logs do SettingSync-Azure, a ID do evento 6013 com o erro 80070259 é vista com frequência.

**Ação recomendada**  
Verifique se o cliente do Windows 10 v1607 tem a atualização cumulativa de 23 de agosto de 2016 ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393,82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Os favoritos do Internet Explorer não sincronizam

Afeta os dispositivos que executam a atualização de novembro do Windows 10 (versão 1511).

**Ação recomendada**  
Verifique se o cliente do Windows 10 v1511 tem a atualização cumulativa de julho de 2016 ([KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586,494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>O tema não está sincronizando, bem como os dados protegidos com a proteção de informações do Windows 

Para evitar o vazamento de dados, os dados protegidos com a [proteção de informações do Windows](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) não serão sincronizados por meio do Enterprise State roaming para dispositivos que usam a atualização de aniversário do Windows 10.

**Ação recomendada**  
Nenhum. Atualizações futuras do Windows podem resolver esse problema.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>As configurações de data, hora e região não são sincronizadas no dispositivo ingressado no domínio 
  
Os dispositivos que ingressaram no domínio não terão uma sincronização para a configuração data, hora e região: tempo automático. Usar o tempo automático pode substituir as outras configurações de data, hora e região e fazer com que essas configurações não sejam sincronizadas. 

**Ação recomendada**  
Nenhum. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>O UAC solicita ao sincronizar senhas

Afeta os dispositivos que executam a atualização de novembro do Windows 10 (versão 1511) com uma NIC sem fio configurada para sincronizar senhas.

**Ação recomendada**  
Verifique se o cliente do Windows 10 v1511 tem a atualização cumulativa ([KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586,494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>A sincronização não funciona em dispositivos que usam o cartão inteligente para logon

Se você tentar entrar em seu dispositivo Windows usando um cartão inteligente ou um cartão inteligente virtual, a sincronização de configurações deixará de funcionar.     

**Ação recomendada**  
Nenhum. Atualizações futuras do Windows podem resolver esse problema.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>O dispositivo ingressado no domínio não está sincronizando depois de sair da rede corporativa     

Os dispositivos ingressados no domínio registrados no Azure AD poderão apresentar falha de sincronização se o dispositivo estiver fora do local por longos períodos de tempo e a autenticação de domínio não puder ser concluída.

**Ação recomendada**  
Conecte o dispositivo a uma rede corporativa para que a sincronização possa ser retomada.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>O dispositivo ingressado no Azure AD não está sincronizando e o usuário tem um nome de entidade de usuário de caso misto.

Se o usuário tiver um UPN com maiúsculas e minúsculas (por exemplo, nome de usuário em vez de nome de usuário) e o usuário estiver em um dispositivo ingressado no Azure AD que tenha sido atualizado do Windows 10 Build 10586 para 14393, o dispositivo do usuário poderá falhar ao ser sincronizado. 

**Ação recomendada**  
O usuário precisará desassociar e reingressar o dispositivo na nuvem. Para fazer isso, faça logon como o usuário administrador local e desingresse o dispositivo acessando **configurações** > **sistema** > **sobre** e selecione "gerenciar ou desconectar do trabalho ou da escola". Limpe os arquivos abaixo e, em seguida, ingresse o dispositivo novamente em **configurações** ** >  > do** **sistema** e selecione "conectar-se ao trabalho ou à escola". Continue a ingressar o dispositivo para Azure Active Directory e concluir o fluxo.

Na etapa de limpeza, limpe os seguintes arquivos:
- Settings. dat no `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Todos os arquivos na pasta `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID do evento 6065:80070533 este usuário não pode entrar porque esta conta está desabilitada no momento  

Em Visualizador de Eventos nos logs de SettingSync/depuração, esse erro pode ser visto quando as credenciais do usuário expiraram. Além disso, isso pode ocorrer quando o locatário não tiver automaticamente o AzureRMS provisionado. 

**Ação recomendada**  
No primeiro caso, faça com que o usuário atualize suas credenciais e faça logon no dispositivo com as novas credenciais. Para resolver o problema do AzureRMS, prossiga com as etapas listadas em [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>ID do evento 1098: erro: falha na operação do agente de token 0xCAA5001C  

Em Visualizador de Eventos nos logs do AAD/operacional, esse erro pode ser visto com o evento 1104: chamada de plug-in do AAD Cloud AP o token Get retornou o erro: 0xC000005F. Esse problema ocorre se houver permissões ou atributos de propriedade ausentes.  

**Ação recomendada**  
Continue com as etapas listadas [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Passos seguintes

Para obter uma visão geral, consulte [visão geral do Enterprise State roaming](enterprise-state-roaming-overview.md).
