---
title: Azure Ative Directory gestão de dispositivos FAQ | Microsoft Docs
description: Azure Ative Directory gestão de dispositivos FAQ.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb468dba1156087bdeb6086f5bc24b214e576e18
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773305"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Ative Directory gestão de dispositivos FAQ

## <a name="general-faq"></a>FAQs Gerais

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>P: Registei o dispositivo recentemente. Por que não consigo ver o dispositivo sob a informação do meu utilizador no portal Azure? Ou porque é que o proprietário do dispositivo está marcado como N/A para dispositivos híbridos Azure Ative Directory (Azure AD) ?

**A:** Os dispositivos do Windows 10 que são híbridos Azure AD não aparecem nos **dispositivos user**.
Utilize a vista **todos os dispositivos** no portal Azure. Também pode utilizar um cmdlet PowerShell [Get-MsolDevice.](/powershell/module/msonline/get-msoldevice)

Apenas os seguintes dispositivos estão listados em **dispositivos USER:**

- Todos os dispositivos pessoais que não são híbridos Azure AD juntou-se. 
- Todos os dispositivos não Windows 10 ou Windows Server 2016.
- Todos os dispositivos não-Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>P: Como sei qual é o estado de registo do dispositivo do cliente?

**A:** No portal Azure, aceda a **Todos os dispositivos.** Procure o dispositivo utilizando o ID do dispositivo. Verifique o valor por baixo da coluna do tipo de junção. Por vezes, o dispositivo pode ser reiniciado ou remimagem. Por isso, é essencial também verificar o estado de registo do dispositivo no dispositivo:

- Para dispositivos Windows 10 e Windows Server 2016 ou posteriores, executar `dsregcmd.exe /status` .
- Para versões de SO de nível inferior, corra `%programFiles%\Microsoft Workplace Join\autoworkplace.exe` .

**A:** Para obter informações sobre resolução de problemas, consulte estes artigos:
- [Dispositivos de resolução de problemas utilizando comando dsregcmd](troubleshoot-device-dsregcmd.md)
- [O Azure Ative Directy híbrido de resolução de problemas juntou-se aos dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [O Azure Ative Directory híbrido de resolução de problemas juntou-se a dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>P: Vejo o registo do dispositivo sob a informação do UTILIZADOR no portal Azure. E vejo o estado como registado no dispositivo. Estou preparado para usar o Acesso Condicional?

**A:** O estado de adesão do dispositivo, mostrado pelo **deviceID,** deve corresponder ao estado em Azure AD e cumprir quaisquer critérios de avaliação para acesso condicional. Para obter mais informações, consulte [Dispositivos geridos para acesso a aplicações na nuvem com Acesso Condicional.](../conditional-access/require-managed-devices.md)

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>P: Porque é que os meus utilizadores vêem uma mensagem de erro a dizer "A sua organização apagou o dispositivo" ou "A sua organização desativou o dispositivo" nos seus dispositivos Windows 10?

**A:** Nos dispositivos do Windows 10 ligados ou registados no AZure AD, os utilizadores recebem um [token de atualização primária (PRT)](concept-primary-refresh-token.md) que permite um único sinal. A validade do PRT baseia-se na validade do próprio dispositivo. Os utilizadores vêem esta mensagem se o dispositivo for eliminado ou desativado em AZure AD sem iniciar a ação a partir do próprio dispositivo. Um dispositivo pode ser eliminado ou desativado em Azure AD um dos seguintes cenários: 

- O utilizador desativa o dispositivo a partir do portal My Apps. 
- Um administrador (ou utilizador) elimina ou desativa o dispositivo no portal Azure ou através da utilização do PowerShell
- A AD Híbrida AD unô-se apenas: Um administrador remove os dispositivos OU fora do âmbito de sincronização, resultando na eliminação dos dispositivos do Azure AD
- Atualização Azure AD liga-se à versão 1.4.xx.x. [Compreender o Azure AD Connect 1.4.xx.x e o desaparecimento do dispositivo](../hybrid/reference-connect-device-disappearance.md).


Veja abaixo como estas ações podem ser retificadas.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>P: Desativei ou eliminei o meu dispositivo no portal Azure ou utilizando o Windows PowerShell. Mas o estado local do dispositivo diz que ainda está registado. O que devo fazer?

**A:** Esta operação é por desígnio. Neste caso, o dispositivo não tem acesso a recursos na nuvem. Os administradores podem executar esta ação para dispositivos perdidos ou roubados para impedir o acesso não autorizado. Se esta ação foi realizada sem querer, terá de voltar a ativar ou re-registar o dispositivo, conforme descrito abaixo.

- Se o dispositivo foi desativado em Azure AD, um administrador com privilégios suficientes pode ativá-lo a partir do portal AD AZure  
  > [!NOTE]
  > Se estiver a sincronizar dispositivos utilizando o Azure AD Connect, os dispositivos híbridos Azure AD serão automaticamente re-activados durante o próximo ciclo de sincronização. Por isso, se precisar de desativar um dispositivo híbrido Azure AD, tem de o desativar a partir do seu AD no local.

 - Se o dispositivo for eliminado no Azure AD, é necessário voltar a registar o dispositivo. Para voltar a registar-se, tem de tomar uma ação manual no aparelho. Consulte abaixo as instruções de reins registo com base no estado do dispositivo. 

      Para re-registar os dispositivos híbridos Azure AD aderidos ao Windows 10 e Windows Server 2016/2019, tome as seguintes medidas:

      1. Abra o pedido de comando como administrador.
      1. Introduza `dsregcmd.exe /debug /leave`.
      1. Inscreva-se e inscreva-se para desencadear a tarefa programada que regista novamente o dispositivo com a Azure AD. 

      Para versões de nível inferior do Windows OS que são ad AD híbridas Azure, tome os seguintes passos:

      1. Abra o pedido de comando como administrador.
      1. Introduza `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Introduza `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Para os dispositivos Azure AD associados aos dispositivos Windows 10, tome os seguintes passos:

      1. Abra o pedido de comando como administrador
      1. Insira `dsregcmd /forcerecovery` (Nota: Tem de ser administrador para realizar esta ação).
      1. Clique em "Iniciar sin" no diálogo que se abre e continue com o sinal em processo.
      1. Assine e assine na parte de trás do dispositivo para completar a recuperação.

      Para os dispositivos Azure AD registados no Windows 10, tome os seguintes passos:

      1. Ir para **As Definições**  >  **Contas**  >  **Aceder trabalho ou escola**. 
      1. Selecione a conta e **selecione Desligar**.
      1. Clique em "+ Connect" e registe novamente o dispositivo através da placa em processo.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>P: Por que vejo entradas de dispositivos duplicados no portal Azure?

**A:**

- No que diz no Windows 10 e Windows Server 2016, as tentativas repetidas de não se unirem e voltarem a juntar-se ao mesmo dispositivo podem causar entradas duplicadas. 
- Cada utilizador do Windows que utiliza **o Add Work ou a Conta Escolar** cria um novo registo de dispositivo com o mesmo nome do dispositivo.
- Para versões de nível inferior do Windows OS que estão no local Azure Directy domínio aderido, o registo automático cria um novo registo do dispositivo com o mesmo nome de dispositivo para cada utilizador de domínio que se inscreve no dispositivo. 
- Um AD AD Azure juntou-se a uma máquina que foi limpa, reinstalada e reunida com o mesmo nome e aparece como outro registo com o mesmo nome do dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>P: O registo do dispositivo Windows 10 no Azure AD suporta TPMs no modo FIPS?

**A:** O registo do dispositivo Windows 10 apenas suportado para TPM 2.0 em conformidade com o FIPS e não suportado para TPM 1.2. Se os seus dispositivos tiverem TPM 1.2 compatível com FIPS, deve desativá-los antes de prosseguir com a aderente AZure AD ou a ad AD híbrido. A Microsoft não fornece quaisquer ferramentas para desativar o modo FIPS para TPMs, uma vez que depende do fabricante TPM. Contacte o seu hardware OEM para obter suporte. 

---

**P: Porque é que um utilizador ainda pode aceder aos recursos a partir de um dispositivo que desativei no portal Azure?**

**A:** Demora até uma hora para que uma revogação seja aplicada a partir do momento em que o dispositivo Azure AD é marcado como desativado.

>[!NOTE] 
>Para os dispositivos inscritos, recomendamos que limpe o dispositivo para garantir que os utilizadores não podem aceder aos recursos. Para mais informações, veja [o que é a inscrição do dispositivo?](/mem/intune/user-help/use-managed-devices-to-get-work-done) 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>P: Por que existem dispositivos marcados como "Pendentes" sob a coluna REGISTADA no portal Azure?

**A**: Pendente indica que o dispositivo não está registado. Este estado indica que um dispositivo foi sincronizado utilizando a ligação Azure AD a partir de um AD no local e está pronto para o registo do dispositivo. Estes dispositivos têm o conjunto TIPO JOIN para "Hybrid Azure AD aderido". Saiba mais sobre [como planear o seu Azure Ative Directory híbrido aderir à implementação.](hybrid-azuread-join-plan.md)

>[!NOTE]
>Um dispositivo também pode mudar de ter um estado registado para "Pendente"
>* Se um dispositivo for eliminado primeiro do AZure AD e re-sincronizado a partir de um AD no local.
>* Se um dispositivo for removido de um âmbito de sincronização no Azure AD Connect e adicionado de volta.
>
>Em ambos os casos, deve voltar a registar o dispositivo manualmente em cada um destes dispositivos. Para rever se o dispositivo foi previamente registado, é possível que [os dispositivos de resolução de problemas utilizem o comando dsregcmd](troubleshoot-device-dsregcmd.md).

---

### <a name="q-i-cannot-add-more-than-3-azure-ad-user-accounts-under-the-same-user-session-on-a-windows-10-device-why"></a>P: Não posso adicionar mais de 3 contas de utilizadores AZure AD na mesma sessão de utilizadores num dispositivo Windows 10, porquê?

**R**: Azure AD adicionou suporte para várias contas AD Azure na versão do Windows 10 1803. No entanto, o Windows 10 restringe o número de contas AD AZure num dispositivo a 3 para limitar o tamanho dos pedidos de token e permitir um único sinal fiável (SSO). Uma vez adicionadas 3 contas, os utilizadores verão um erro nas contas subsequentes. As informações adicionais sobre problemas no ecrã de erro fornecem a seguinte mensagem indicando o motivo - "Adicionar a operação da conta está bloqueada porque o limite de conta é atingido". 

---
## <a name="azure-ad-join-faq"></a>Azure AD junta-se à FAQ

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>P: Como é que eu não me uniria a um AD AZure ligado localmente ao dispositivo?

**A:** Para dispositivos puros Azure AD, certifique-se de que tem uma conta de administrador local offline ou crie uma. Não pode iniciar súmia com credenciais de utilizador da AZure. Em seguida, vá ao Trabalho de Acesso de **Contas de Definições**  >    >  **ou Escola.** Selecione a sua conta e **selecione Desligar**. Siga as instruções e forneça as credenciais do administrador local quando solicitado. Reinicie o dispositivo para terminar o processo de unjoin.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>P: O sinal dos meus utilizadores no Azure AD pode juntar-se a dispositivos que são eliminados ou desativados em Azure AD?

**R:** Sim. O Windows tem um nome de utilizador em cache e uma capacidade de senha que permite aos utilizadores que se inscreveram anteriormente acederem rapidamente ao ambiente de trabalho, mesmo sem conectividade de rede. 

Quando um dispositivo é eliminado ou desativado em Azure AD, não é do conhecimento do dispositivo Windows. Assim, os utilizadores que se inscreveram anteriormente continuam a aceder ao ambiente de trabalho com o nome de utilizador e senha em cache. Mas como o dispositivo é eliminado ou desativado, os utilizadores não podem aceder a quaisquer recursos protegidos pelo Acesso Condicional baseado no dispositivo. 

Os utilizadores que não assinaram anteriormente não conseguem aceder ao dispositivo. Não há nome de utilizador em cache e senha ativada para eles. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>P: Pode um utilizador desativado ou eliminado entrar em dispositivos azure AD

**A:** Sim, mas só por um tempo limitado. Quando um utilizador é eliminado ou desativado em Azure AD, não é imediatamente conhecido do dispositivo Windows. Assim, os utilizadores que se inscreveram anteriormente podem aceder ao ambiente de trabalho com o nome de utilizador e senha em cache. 

Normalmente, o dispositivo está ciente do estado do utilizador em menos de quatro horas. Em seguida, o Windows bloqueia o acesso dos utilizadores ao ambiente de trabalho. Uma vez que o utilizador é eliminado ou desativado em Azure AD, todos os seus tokens são revogados. Para que não possam aceder a recursos. 

Os utilizadores eliminados ou desativados que não assinaram anteriormente não conseguem aceder a um dispositivo. Não há nome de utilizador em cache e senha ativada para eles. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>P: Porque é que os meus utilizadores têm problemas em dispositivos aderidos ao Azure AD depois de mudarem a SUA UPN?

**A:** Atualmente, as alterações da UPN não são totalmente suportadas em dispositivos aderidos a Azure AD. Assim, a sua autenticação com Azure AD falha após as alterações da UPN. Como resultado, os utilizadores têm problemas de SSO e Acesso Condicionado nos seus dispositivos. Neste momento, os utilizadores precisam de iniciar sedições no Windows através do azulejo "Outro utilizador" utilizando a sua nova UPN para resolver este problema. Estamos neste momento a trabalhar na abordagem desta questão. No entanto, os utilizadores que se inscrevam no Windows Hello for Business não enfrentam este problema. 

As alterações da UPN são suportadas com a atualização do Windows 10 2004. Os utilizadores em dispositivos com esta atualização não terão problemas após a alteração das suas UPNs

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>P: Os meus utilizadores não podem pesquisar impressoras a partir de dispositivos aderidos a Azure AD. Como posso permitir a impressão a partir desses dispositivos?

**A:** Para implementar impressoras para dispositivos AD AZure, consulte [implementar a impressão híbrida do Servidor do Windows com pré-autenticação](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Precisa de um Windows Server no local para implementar a impressão em nuvem híbrida. Atualmente, o serviço de impressão baseado na nuvem não está disponível. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>P: Como posso ligar-me a um dispositivo remoto Azure AD?

**A:** Consulte [Connect para pc remoto Azure Ative Directory .](/windows/client-management/connect-to-remote-aadj-pc)

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>P: Porque é que os meus utilizadores vêem *que não consegues chegar lá a partir daqui?*

**A:** Configuraste certas regras de acesso condicional para exigir um estado específico do dispositivo? Se o dispositivo não cumprir os critérios, os utilizadores ficam bloqueados e vêem essa mensagem. Avaliar as regras da política de acesso condicional. Certifique-se de que o aparelho satisfaz os critérios para evitar a mensagem.

---

### <a name="q-why-dont-some-of-my-users-get-azure-ad-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>P: Porque é que alguns dos meus utilizadores não recebem pedidos de autenticação multi-factor Azure AD em dispositivos ligados ao Azure AD?

**A:** Um utilizador pode juntar-se ou registar um dispositivo com Azure AD utilizando a Autenticação Multi-Factor. Em seguida, o próprio dispositivo torna-se um segundo fator de confiança para esse utilizador. Sempre que o mesmo utilizador entra no dispositivo e acede a uma aplicação, a Azure AD considera o dispositivo como um segundo fator. Permite ao utilizador aceder perfeitamente a aplicações sem solicitações adicionais de autenticação multi-factor. 

Este comportamento:

- É aplicável aos dispositivos registados Azure AD e Azure AD - mas não para dispositivos híbridos Azure AD.
- Não é aplicável a qualquer outro utilizador que se inscreve nesse dispositivo. Assim, todos os outros utilizadores que acedem a este dispositivo recebem um desafio de autenticação multi-factor. Em seguida, podem aceder a aplicações que requerem autenticação multi-factor.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>P: Por que o *nome de utilizador ou palavra-passe é* uma mensagem incorreta para um dispositivo que acabei de aderir ao Azure AD?

**A:** As razões comuns para este cenário são as seguintes:

- As suas credenciais de utilizador já não são válidas.
- O seu computador não consegue comunicar com o Azure Ative Directory. Verifique se há problemas de conectividade de rede.
- As inscrições federadas exigem que o servidor da federação suporte WS-Trust pontos finais que estejam ativados e acessíveis. 
- Habilitado a autenticação pass-through. Portanto, a sua senha temporária precisa de ser alterada quando iniciar sôs o seu sôm.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>P: Por que vejo os *Oops... Ocorreu um erro!* diálogo quando tento a Azure AD juntar-se ao meu PC?

**A:** Este erro ocorre quando configura a inscrição do Azure Ative Directory com o Intune. Certifique-se de que o utilizador que tenta aderir à Azure AD tem a licença Intune correta atribuída. Para obter mais informações, consulte [configurar a inscrição para dispositivos Windows](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>P: Porque é que a minha tentativa de Azure AD se juntar a um PC falhou, embora eu não tenha conseguido nenhuma informação de erro?

**A:** Uma causa provável é que tenha assinado o dispositivo usando a conta de administrador incorporada local. Crie uma conta local diferente antes de usar o Azure Ative Directory para terminar a configuração. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>P: Quais são os certificados MS-Organization-P2P-Access presentes nos nossos dispositivos Windows 10?

**A:** Os certificados MS-Organization-P2P-Access são emitidos pela Azure AD para ambos, Azure AD unidos e híbrido Azure AD dispositivos de ad. Estes certificados são usados para permitir a confiança entre dispositivos no mesmo inquilino para cenários remotos de ambiente de trabalho. Um certificado é emitido para o dispositivo e outro é emitido ao utilizador. O certificado do dispositivo está presente `Local Computer\Personal\Certificates` e é válido por um dia. Este certificado é renovado (emitindo um novo certificado) se o dispositivo ainda estiver ativo no Azure AD. O certificado de utilizador está presente `Current User\Personal\Certificates` e este certificado também é válido por um dia, mas é emitido a pedido quando um utilizador tenta uma sessão de desktop remota para outro dispositivo aderido a Azure AD. Não é renovado no termo. Ambos os certificados são emitidos utilizando o certificado MS-Organização-P2P-Access presente no `Local Computer\AAD Token Issuer\Certificates` . Este certificado é emitido pela Azure AD durante o registo do dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>P: Por que vejo vários certificados expirados emitidos pela MS-Organization-P2P-Access nos nossos dispositivos Windows 10? Como posso apagá-los?

**A:** Havia um problema identificado na versão 1709 do Windows 10 e inferior onde os certificados MS-Organization-P2P-Access expirados continuavam a existir na loja de computadores devido a problemas criptográficos. Os seus utilizadores poderão enfrentar problemas com a conectividade da rede, se estiver a utilizar clientes VPN (por exemplo, Cisco AnyConnect) que não consigam lidar com o grande número de certificados expirados. Este problema foi corrigido na versão do Windows 10 1803 para eliminar automaticamente quaisquer certificados de MS-Organization-P2P-Access expirados. Pode resolver este problema atualizando os seus dispositivos para o Windows 10 1803. Se não conseguir atualizar, pode eliminar estes certificados sem qualquer impacto adverso.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Hybrid Azure AD junta-se à FAQ

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>P: Como é que eu não me acossondo um AD Híbrido Ad aderido localmente no dispositivo?

**A:** Para dispositivos híbridos Azure AD, certifique-se de desligar o registo automático. Em seguida, a tarefa programada não regista o dispositivo novamente. Em seguida, abra um pedido de comando como administrador e entre `dsregcmd.exe /debug /leave` . Ou executar este comando como um script em vários dispositivos para descomprissar a granel.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>P: Onde posso encontrar informações de resolução de problemas para diagnosticar falhas híbridas da Azure AD?

**A:** Para obter informações sobre resolução de problemas, consulte estes artigos:

- [O Azure Ative Directy híbrido de resolução de problemas juntou-se aos dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [O Azure Ative Directory híbrido de resolução de problemas juntou-se a dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>P: Por que vejo um registo AZure AD duplicado para o meu dispositivo híbrido Azure AD do Windows 10 na lista de dispositivos AD do Azure?

**A:** Quando os seus utilizadores adicionam as suas contas a apps num dispositivo ligado a domínios, podem ser solicitados com **a conta Add ao Windows?** Se introduzirem **Sim** na solicitação, o dispositivo regista-se com Azure AD. O tipo de confiança é marcado como Azure AD registado. Depois de ativar a adada híbrida Azure na sua organização, o dispositivo também recebe a ad AD híbrida. Então dois estados de dispositivo aparecem para o mesmo dispositivo. 

Na maioria dos casos, a adesão da Hybrid Azure AD tem precedência sobre o estado registado Azure AD, resultando em que o seu dispositivo seja considerado híbrido Azure AD aderido para qualquer avaliação de autenticação e acesso condicional. No entanto, por vezes, este duplo estado pode resultar numa avaliação não determinante do dispositivo e causar problemas de acesso. Recomendamos vivamente o upgrade para a versão 1803 do Windows 10 e acima, onde limpamos automaticamente o estado registado Azure AD. Saiba como [evitar ou limpar este duplo estado na máquina do Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>P: Porque é que os meus utilizadores têm problemas nos dispositivos híbridos Azure AD híbridos do Windows 10 depois de mudarem a SUA UPN?

**A:** Atualmente, as alterações da UPN não são totalmente suportadas com dispositivos híbridos Azure AD. Enquanto os utilizadores podem iniciar súmia no dispositivo e aceder às suas aplicações no local, a autenticação com Azure AD falha após uma alteração da UPN. Como resultado, os utilizadores têm problemas de SSO e Acesso Condicionado nos seus dispositivos. Neste momento, é necessário desagregá-lo a partir do Azure AD (executar "dsregcmd/leave" com privilégios elevados) e voltar a juntar-se (acontece automaticamente) para resolver o problema. Estamos neste momento a trabalhar na abordagem desta questão. No entanto, os utilizadores que se inscrevam no Windows Hello for Business não enfrentam este problema. 

As alterações da UPN são suportadas com a atualização do Windows 10 2004. Os utilizadores em dispositivos com esta atualização não terão problemas após a alteração das suas UPNs

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>P: Os dispositivos híbridos AZure AD híbridos do Windows 10 requerem linha de visão ao controlador de domínio para ter acesso aos recursos na nuvem?

**A:** Não, exceto quando a palavra-passe do utilizador é alterada. Depois de a ad join AZure AD híbrida do Windows 10 estar concluída, e o utilizador ter assinado pelo menos uma vez, o dispositivo não necessita de uma linha de visão para o controlador de domínio para aceder aos recursos na nuvem. O Windows 10 pode obter um único sinal de acesso às aplicações AD do Azure de qualquer lugar com uma ligação à Internet, exceto quando uma palavra-passe é alterada. Os utilizadores que iniciarem sedúns com o Windows Hello for Business continuam a obter um único sinal de acesso às aplicações AD do Azure mesmo depois de uma alteração de palavra-passe, mesmo que não tenham linha de visão para o seu controlador de domínio. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>P: O que acontece se um utilizador alterar a sua palavra-passe e tentar iniciar sessão no seu dispositivo híbrido Azure AD híbrido do Windows 10 fora da rede corporativa?

**A:** Se uma palavra-passe for alterada fora da rede corporativa (por exemplo, utilizando o Azure AD SSPR), então o utilizador iniciará singing com a nova palavra-passe. Para os dispositivos híbridos Azure AD, o Ative Directory no local é a principal autoridade. Quando um dispositivo não tem linha de visão para o controlador de domínio, não é possível validar a nova palavra-passe. Assim, o utilizador precisa estabelecer a ligação com o controlador de domínio (seja através da VPN ou estar na rede corporativa) antes de poder iniciar sôs no dispositivo com a sua nova senha. Caso contrário, só podem iniciar sação com a sua antiga palavra-passe devido à capacidade de sílpecla em cache no Windows. No entanto, a senha antiga é invalidada pela Azure AD durante os pedidos simbólicos e, portanto, impede uma única sação e falha em quaisquer políticas de Acesso Condicional baseadas no dispositivo. Este problema não ocorre se utilizar o Windows Hello para negócios. 

---

## <a name="azure-ad-register-faq"></a>Azure AD registra FAQ

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>P: Como remover um estado registado AZure AD para um dispositivo localmente?

**A:** 
- Para dispositivos registados no Windows 10 AZure AD, vá a **Definições**  >  **Contas**  >  **Access Work ou School**. Selecione a sua conta e **selecione Desligar**. O registo do dispositivo é por perfil do utilizador no Windows 10.
- Para iOS e Android, pode utilizar o registo de **dispositivos de definição de configurações** da aplicação Microsoft Authenticator  >   e selecionar o **dispositivo Unregister**.
- Para o macOS, pode utilizar a aplicação Microsoft Intune Company Portal para desativar o dispositivo da gestão e remover qualquer registo. 

Para dispositivos Windows 10, este processo pode ser automatizado com a [ferramenta de remoção workplace Join (WPJ)](https://download.microsoft.com/download/8/e/f/8ef13ae0-6aa8-48a2-8697-5b1711134730/WPJCleanUp.zip)

> [!NOTE]
> Esta ferramenta remove todas as contas SSO do dispositivo. Após esta operação, todas as aplicações perderão o estado SSO, e o dispositivo será desenrolado a partir de ferramentas de gestão (MDM) e não registado a partir da nuvem. Da próxima vez que uma aplicação tentar entrar, os utilizadores serão convidados a adicionar novamente a conta.

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>P: Como posso impedir os utilizadores de adicionarem contas de trabalho adicionais (Azure AD registadas) nos meus dispositivos corporativos do Windows 10?

**A:** Ativar o seguinte registo para impedir que os seus utilizadores adicionem contas de trabalho adicionais ao seu domínio corporativo, a Azure AD juntou-se ou o Azure AD híbrido juntou-se aos dispositivos Windows 10. Esta política também pode ser usada para bloquear máquinas unidas de domínio de obter inadvertidamente AZure AD registrado com a mesma conta de utilizador. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>P: Posso registar dispositivos Android ou iOS BYOD?

**A:** Sim, mas apenas com o serviço de registo de dispositivos Azure e para clientes híbridos. Não é suportado com o serviço de registo de dispositivos no local nos Serviços da Federação de Diretório Ativo (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>P: Como posso registar um dispositivo macOS?

**A:** Tome os seguintes passos:

1.    [Criar uma política de conformidade](/intune/compliance-policy-create-mac-os)
1.    [Defina uma política de acesso condicional para dispositivos macOS](../conditional-access/overview.md) 

**Observações:**

- Os utilizadores incluídos na sua política de Acesso Condicional precisam de uma [versão suportada do Office para que o macOS](../conditional-access/concept-conditional-access-conditions.md) aceda aos recursos. 
- Durante a primeira tentativa de acesso, os seus utilizadores são solicitados a inscrever o dispositivo utilizando o portal da empresa.

---
## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os dispositivos registados Azure AD](concept-azure-ad-register.md)
- Saiba mais sobre [dispositivos aderidos a Azure AD](concept-azure-ad-join.md)
- Saiba mais sobre [dispositivos híbridos Azure AD](concept-azure-ad-join-hybrid.md)
