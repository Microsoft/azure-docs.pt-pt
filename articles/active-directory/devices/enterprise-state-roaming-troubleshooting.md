---
title: Definições de Roaming de estado empresarial no Azure Active Directory de resolução de problemas | Documentos da Microsoft
description: Fornece respostas a algumas perguntas que os administradores de TI podem ter sobre as definições e sincronização de dados de aplicações.
services: active-directory
keywords: Enterprise de estado as configurações de roaming, cloud do windows, perguntas mais frequentes sobre o roaming de estado empresarial
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: devices
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e2b99871cc1da2b1e8e136fc4d689e90dfad77a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081241"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Resolução de problemas de definições de Roaming de estado empresarial no Azure Active Directory

Este tópico fornece informações sobre como solucionar problemas e diagnosticar problemas com o Roaming de estado empresarial e fornece uma lista dos problemas conhecidos.

## <a name="preliminary-steps-for-troubleshooting"></a>Etapas preliminares para resolução de problemas 

Antes de iniciar a resolução de problemas, certifique-se de que o utilizador e dispositivo foram configuradas corretamente e que todos os requisitos de Roaming de estado empresarial são cumpridos, o dispositivo e o utilizador. 

1. Windows 10, com as atualizações mais recentes e um mínimo versão 1511 (compilação do SO 10586 ou posterior) está instalado no dispositivo. 
1. O dispositivo é o Azure AD associado ou Azure AD híbrido associou. Para obter mais informações, consulte [como obter um dispositivo sob o controlo do Azure AD](overview.md).
1. Certifique-se de que **Roaming de estado empresarial** está ativada para o inquilino no Azure AD, tal como descrito na [para ativar o Roaming de estado empresarial](enterprise-state-roaming-enable.md). Pode ativar o roaming para todos os utilizadores ou para um grupo selecionado de utilizadores.
1. Já tem de ser atribuída ao utilizador uma licença do Azure Active Directory Premium.  
1. O dispositivo tem de ser reiniciado e o utilizador tem de iniciar sessão novamente para aceder às funcionalidades de Roaming de estado empresarial.

## <a name="information-to-include-when-you-need-help"></a>Informações a incluir quando precisar de ajuda
Se não conseguir resolver o seu problema com as orientações abaixo, pode contactar os nossos engenheiros de suporte. Quando entre em contato com eles, incluem as seguintes informações:

* **Descrição geral do erro**: Existem mensagens de erro vistas pelo utilizador? Caso não tenha havido nenhuma mensagem de erro, descreva o comportamento inesperado, que reparou em detalhes. Que funcionalidades estão ativadas para sincronização e o que é o utilizador à espera de sincronização? São vários recursos não sincronizar ou está isolado numa?
* **Utilizadores afetados** – está sincronizado trabalho/falhados para um utilizador ou de vários usuários? Quantos dispositivos estão envolvidos por utilizador? São todos eles não sincronizar ou são alguns deles, sincronização e alguns não sincronizar?
* **Informações sobre o utilizador** – o que a identidade é o utilizador a utilizar para iniciar sessão no dispositivo? Como é o utilizador iniciar sessão no dispositivo? Eles são parte de um grupo de segurança selecionado permitido para sincronizar? 
* **Informações sobre o dispositivo** – este dispositivo do Azure AD associado ou associado a um domínio? Que compilação o dispositivo está no? Quais são as atualizações mais recentes?
* **Data / hora / fuso horário** – qual era o precisa data e hora que viu o erro (incluem o fuso horário)?

Incluindo estas informações ajuda-na resolver o seu problema mais depressa possível.

## <a name="troubleshooting-and-diagnosing-issues"></a>Resolução de problemas e diagnosticar problemas
Esta secção fornece sugestões sobre como solucionar problemas e diagnosticar problemas relacionados com Roaming de estado empresarial.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Certifique-se de sincronização e a página de definições "Sincronizar as definições" 

1. Depois de associar o seu PC com Windows 10 a um domínio que está configurado para permitir Roaming de estado empresarial, início de sessão com a sua conta profissional. Aceda a **definições** > **contas** > **definições de sincronização Your** e confirme que a sincronização e as definições individuais são e que a parte superior do página de definições indica que estão a ser sincronizados com a sua conta profissional. Confirmar a mesma conta também é utilizada como a sua conta de início de sessão no **configurações** > **contas** > **informações Your**. 
1. Certifique-se de que a sincronização funciona em várias máquinas, fazendo algumas alterações no computador original, como mover a barra de tarefas para o lado direito ou superior do ecrã. Assista a alteração se propaguem para a segunda máquina dentro de cinco minutos. 

   * Bloqueando e desbloqueando a tela (Win + L) pode ajudar a acionar a sincronização.
   * Deve estar a iniciar com a mesma conta em ambos os PCs para sincronização se trabalhar – como o Roaming de estado empresarial é vinculado à conta de utilizador e não uma conta de computador.

**Problema em potencial**: Se os controles a **definições** página não estão disponíveis e verá a mensagem "o algumas funcionalidades do Windows só estão disponíveis se estiver a utilizar uma conta Microsoft ou a conta profissional". Este problema pode surgir para dispositivos que estão configurados para ser associado a um domínio e registado para o Azure AD, mas o dispositivo ainda não ainda com êxito foi autenticado com o Azure AD. Uma causa possível é que tem de ser aplicada a política de dispositivo, mas esta aplicação acontece de forma assíncrona e poderia ser atrasada por algumas horas. 

### <a name="verify-the-device-registration-status"></a>Verificar o estado de registo do dispositivo

Roaming de estado empresarial requer que o dispositivo ser registado com o Azure AD. Embora não seja específico para o Roaming de estado empresarial, seguindo as instruções abaixo pode ajudar a confirmar que o cliente do Windows 10 está registado e confirmar o estado do thumbprint, URL de definições do Azure AD, NGC e outras informações.

1.  Abra o prompt de comando desligado. Para fazer isso no Windows, abra o iniciador de execução (Win + R) e escreva "cmd" para abrir.
2.  Assim que a linha de comandos estiver aberta, escreva "*dsregcmd.exe /status*".
3.  Para o resultado esperado, o **AzureAdJoined** valor do campo deve ser "Sim", **WamDefaultSet** valor do campo deve ser "Sim" e o **WamDefaultGUID** valor do campo deve ser um GUID com "(AzureAd)" no final.

**Problema em potencial**: **WamDefaultSet** e **AzureAdJoined** tem o valor do campo "Não", o dispositivo foi associado a um domínio e registado com o Azure AD e do dispositivo não sincroniza. Se está a mostrar isso, o dispositivo poderá ter de aguardar para a política seja aplicada ou a autenticação do dispositivo falhou ao estabelecer ligação ao Azure AD. O utilizador pode ter de esperar algumas horas para a política seja aplicada. Outros passos de resolução de problemas podem incluir repetir o registo automático ao terminar a sessão e de volta no ou a iniciar a tarefa no agendador de tarefas. Em alguns casos, em execução "*dsregcmd.exe /leave*" numa janela de linha de comandos elevada, reiniciar o computador e tente novamente o registo podem ajudar nessa questão.


**Problema em potencial**: O campo para **SettingsUrl** está vazia e o dispositivo não sincroniza. O utilizador pode ter iniciou sessão no dispositivo antes do Roaming de estado empresarial foi ativada no Portal do Azure Active Directory. Reiniciar o dispositivo e ter o início de sessão do utilizador. Opcionalmente, no portal, tente ter o administrador de TI, navegue até **do Azure Active Directory** > **dispositivos** > **Roaming de estado empresarial** Desativar e reativar **os utilizadores podem sincronizar definições e dados de aplicação em todos os dispositivos**. Uma vez reativada, reiniciar o dispositivo e tem o início de sessão do utilizador. Se isso não resolver o problema **SettingsUrl** pode estar vazio no caso de um certificado de dispositivo. Neste caso, em execução "*dsregcmd.exe /leave*" numa janela de linha de comandos elevada, reiniciar o computador e tente novamente o registo podem ajudar nessa questão.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming de estado empresarial e multi-factor Authentication 

Sob determinadas condições, Roaming de estado empresarial pode não conseguir sincronizar os dados se o Azure multi-factor Authentication está configurado. Para obter detalhes adicionais sobre esses sintomas, consulte o documento de suporte [KB3193683](https://support.microsoft.com/kb/3193683). 

**Problema em potencial**: Se o dispositivo estiver configurado para requerer o multi-factor Authentication no portal do Azure Active Directory, poderá falhar sincronizar definições ao iniciar sessão num dispositivo Windows 10 com uma palavra-passe. Destina-se este tipo de configuração de multi-factor Authentication para proteger uma conta de administrador do Azure. Os utilizadores administradores ainda poderá sincronizar ao iniciar sessão para os dispositivos Windows 10 com o seu Microsoft Passport para o PIN de trabalho ou ao concluir o multi-factor Authentication ao aceder a outros serviços do Azure como o Office 365.

**Problema em potencial**: Sincronização pode falhar se o administrador configura a política de acesso condicional de autenticação do multi-factor dos serviços de Federação do Active Directory e o token de acesso do dispositivo expira. Certifique-se de que iniciar sessão e termine com o Microsoft Passport para o PIN de trabalho ou concluir o multi-factor Authentication ao aceder a outros serviços do Azure como o Office 365.

### <a name="event-viewer"></a>Visualizador de Eventos

Para resolução de problemas avançada, o Visualizador de eventos pode ser utilizado para localizar erros específicos. Estas estão documentadas na tabela abaixo. Os eventos podem ser encontrados no Visualizador de eventos > registos de serviços e aplicações > **Microsoft** > **Windows** > **SettingSync Azure** e para problemas relacionados com a identidade com o sync **Microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Sincronização não funciona em dispositivos que tenham aplicações de sideload usando software MDM

Afeta os dispositivos que executam a atualização de aniversário do Windows 10 (versão 1607). No Visualizador de eventos em registos SettingSync para o Azure, o 6013 de ID de evento com erro 80070259 é vista com freqüência.

**Ação recomendada**  
Certifique-se o cliente do Windows 10 v1607 tem a 23 de Agosto de 2016 a atualização cumulativa ([KB3176934](https://support.microsoft.com/kb/3176934) 14393.82 de compilação do SO). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Não sincronizar favoritos do Internet Explorer

Afeta os dispositivos que executam a atualização de Novembro do Windows 10 (versão 1511).

**Ação recomendada**  
Certifique-se o cliente do Windows 10 v1511 tem Julho de 2016 a atualização cumulativa ([KB3172985](https://support.microsoft.com/kb/3172985) 10586.494 de compilação do SO).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Tema não estiver a sincronizar, bem como os dados protegidos com o Windows Information Protection 

Para evitar a fuga de dados, os dados protegidos com [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) não serão sincronizados através de Roaming de estado empresarial para dispositivos com a atualização de aniversário do Windows 10.

**Ação recomendada**  
Nenhum. As futuras atualizações Windows poderão resolver este problema.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Definições de data, hora e região não são sincronizados no dispositivo associado a um domínio 
  
Dispositivos que estão associados a um domínio não ocorrerá a sincronização para a definição de data, hora e região: tempo automática. Utilizar hora automática pode substituir as outras definições de data, hora e região e fazer com que essas definições não sincronizar. 

**Ação recomendada**  
Nenhum. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>O UAC solicita quando a sincronização de palavras-passe

Afeta os dispositivos que executam a atualização de Novembro do Windows 10 (versão 1511) com um NIC sem fio que está configurado para sincronizar as palavras-passe.

**Ação recomendada**  
Certifique-se o cliente do Windows 10 v1511 tem a atualização cumulativa ([KB3140743](https://support.microsoft.com/kb/3140743) 10586.494 de compilação do SO).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Sincronização não funciona em dispositivos que utilizem smart cards para início de sessão

Se tentar iniciar sessão no seu dispositivo Windows utilizando um smart card ou virtual smart card, a sincronização de definições deixarão de funcionar.     

**Ação recomendada**  
Nenhum. As futuras atualizações Windows poderão resolver este problema.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Dispositivos associados a um domínio não estiver a sincronizar após sair da rede empresarial     

Dispositivos associados a um domínio registados para o Azure AD podem ocorrer falha de sincronização, se o dispositivo está fora do local por longos períodos de tempo e não é possível concluir a autenticação de domínio.

**Ação recomendada**  
Ligue o dispositivo a uma rede empresarial para que possa retomar a sincronização.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Dispositivos no Azure AD associado não estiver a sincronizar e o utilizador tem um nome de Principal de utilizador de maiúsculas misto.

Se o utilizador tem uma maiúsculas e minúsculas UPN (por exemplo, nome de utilizador em vez do nome de utilizador) e o utilizador está num dispositivo do Azure AD associado que tenha atualizado do Windows 10 criar 10586 para 14393, o dispositivo do utilizador poderá não conseguem ser sincronizados. 

**Ação recomendada**  
O utilizador terá de anulação da associação ao e voltar a participar no dispositivo para a cloud. Para fazer isso, inicie sessão como o utilizador de administrador Local e desassocie o dispositivo ao aceder **configurações** > **sistema** > **sobre** e selecione "Gerir ou desligar do trabalho ou escola". Limpar os arquivos abaixo e, em seguida, associação do Azure AD novamente o dispositivo no **configurações** > **sistema** > **sobre** e selecionando "ligar ao trabalho ou Aulas". Continue a associação do dispositivo ao Azure Active Directory e concluir o fluxo.

O passo de limpeza, limpeza os seguinte ficheiros:
- DAT no `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Todos os ficheiros na pasta `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID de evento 6065: 80070533 este utilizador não pode iniciar sessão uma vez que esta conta está atualmente desativada  

No Visualizador de eventos em registos de SettingSync/depuração, este erro pode ser visto quando as credenciais do utilizador expiraram. Além disso, pode ocorrer quando o inquilino não tinha automaticamente AzureRMS aprovisionado. 

**Ação recomendada**  
No primeiro caso, peça ao utilizador atualizar suas credenciais e início de sessão para o dispositivo com as novas credenciais. Para resolver o problema de AzureRMS, continue com os passos listados em [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>ID de evento 1098: Erro: operação de Mediador de token de 0xCAA5001C falhou  

No Visualizador de eventos em registos de AAD/Operational, este erro pode ser visto com 1104 de evento: Chamada de plug-in do AP de nuvem do AAD Get token devolveu o erro: 0xC000005F. Este problema ocorre se estiverem faltando permissões ou atributos de propriedade.  

**Ação recomendada**  
Continue com os passos listados [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Passos Seguintes

Para uma descrição geral, consulte [descrição geral de roaming de estado empresarial](enterprise-state-roaming-overview.md).
