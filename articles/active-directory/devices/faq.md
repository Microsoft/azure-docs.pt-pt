---
title: FaQ de gestão de dispositivos de diretório Ativo Azure Microsoft Docs
description: FaQ de gestão de dispositivos de diretório Ativo Azure.
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
ms.openlocfilehash: f5345a96e333e0f75264880ee18a95c9ab8dd63c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262259"
---
# <a name="azure-active-directory-device-management-faq"></a>FaQ de gestão de dispositivos de diretório ativo Azure

## <a name="general-faq"></a>FaQ Geral

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>P: Registei o dispositivo recentemente. Por que não consigo ver o dispositivo sob a informação do meu utilizador no portal Azure? Ou porque é que o proprietário do dispositivo está marcado como N/A para o azure ative diretório híbrido (Azure AD) que se juntou a dispositivos?

**A:** Os dispositivos do Windows 10 que são híbridos Azure AD não aparecem em **dispositivos USER**.
Utilize a vista **De todos os dispositivos** no portal Azure. Também pode utilizar um cmdlet PowerShell [Get-MsolDevice.](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

Apenas os seguintes dispositivos estão listados nos **dispositivos USER:**

- Todos os dispositivos pessoais que não são híbridos Azure AD juntaram-se. 
- Todos os dispositivos não Windows 10 ou Windows Server 2016.
- Todos os dispositivos não Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>P: Como sei qual é o estado de registo do dispositivo do cliente?

**A:** No portal Azure, vá a **Todos os dispositivos.** Procure o dispositivo utilizando o ID do dispositivo. Verifique o valor sob a coluna do tipo de união. Por vezes, o dispositivo pode ser reiniciado ou reimaged. Por isso, é essencial também verificar o estado de registo do dispositivo no dispositivo:

- Para dispositivos Windows 10 e Windows Server 2016 ou posteriores, execute `dsregcmd.exe /status`.
- Para versões de baixo nível, execute `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

**A:** Para obter informações sobre resolução de problemas, consulte estes artigos:
- [Dispositivos de resolução de problemas utilizando comando dsregcmd](troubleshoot-device-dsregcmd.md)
- [O Diretório Ativo Azure Ative de Resolução de Problemas juntou-se aos dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Resolução de problemas híbrido Azure Ative Directory juntou-se a dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>P: Vejo o registo do dispositivo sob a informação do UTILIZADOR no portal Azure. E vejo o estado registado no dispositivo. Estou preparado corretamente para usar o Acesso Condicional?

**A:** O dispositivo junta-se ao estado, mostrado pelo **dispositivoID,** deve coincidir com o estado em Azure AD e cumprir quaisquer critérios de avaliação para acesso condicional. Para mais informações, consulte [Exigir dispositivos geridos para acesso](../conditional-access/require-managed-devices.md)a aplicações na nuvem com Acesso Condicional .

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>P: Porque é que os meus utilizadores vêem uma mensagem de erro a dizer "A sua organização apagou o dispositivo" ou "A sua organização desativou o dispositivo" nos seus dispositivos Windows 10?

**A:** Nos dispositivos Windows 10 unidos ou registados com a AD Azure, os utilizadores são emitidos um [token de atualização Primary (PRT)](concept-primary-refresh-token.md) que permite um único sinal. A validade do PRT baseia-se na validade do próprio dispositivo. Os utilizadores vêem esta mensagem se o dispositivo for apagado ou desativado em AD Azure sem ativar a ação do próprio dispositivo. Um dispositivo pode ser eliminado ou desativado em Azure AD um dos seguintes cenários: 

- O utilizador desativa o dispositivo a partir do portal My Apps. 
- Um administrador (ou utilizador) elimina ou desativa o dispositivo no portal Azure ou utilizando o PowerShell
- Hybrid Azure AD apenas aderiu: Um administrador remove os dispositivos OU fora do âmbito de sincronização, resultando na eliminação dos dispositivos da Azure AD
- Upgrade Azure AD liga-se à versão 1.4.xx.x. [Compreender o Azure AD Connect 1.4.xx.x e o desaparecimento](/azure/active-directory/hybrid/reference-connect-device-disappearance)do dispositivo .


Veja abaixo como estas ações podem ser retificadas.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>P: Desativei ou apaguei o meu dispositivo no portal Azure ou utilizando o Windows PowerShell. Mas o estado local do dispositivo diz que ainda está registado. O que devo fazer?

**A:** Esta operação é por desígnio. Neste caso, o dispositivo não tem acesso a recursos na nuvem. Os administradores podem realizar esta ação para dispositivos velhos, perdidos ou roubados para impedir o acesso não autorizado. Se esta ação foi realizada sem querer, terá de reativar ou reregistar o dispositivo conforme descrito abaixo

- Se o dispositivo foi desativado em Azure AD, um administrador com privilégios suficientes pode permitir-lhe a partir do portal Azure AD  
  > [!NOTE]
  > Se estiver a sincronizar dispositivos utilizando o Azure AD Connect, os dispositivos híbridos azure ad serão automaticamente reativados durante o próximo ciclo de sincronização. Por isso, se precisar de desativar um dispositivo híbrido Azure AD, precisa desativá-lo a partir do seu ad ad no local

 - Se o dispositivo for eliminado em Azure AD, terá de voltar a registar o dispositivo. Para voltar a registar-se, tem de tomar uma ação manual no dispositivo. Consulte abaixo as instruções para o reregisto com base no estado do dispositivo. 

      Para voltar a registar os dispositivos Híbridos Azure AD juntaram-se aos dispositivos Windows 10 e Windows Server 2016/2019, tome as seguintes etapas:

      1. Abra o pedido de comando como administrador.
      1. Introduza `dsregcmd.exe /debug /leave`.
      1. Inscreva-se e inscreva-se para desencadear a tarefa programada que regista o dispositivo novamente com a AD Azure. 

      Para versões de baixo nível do Windows OS que são híbridas Azure AD, tome os seguintes passos:

      1. Abra o pedido de comando como administrador.
      1. Introduza `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Introduza `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Para o Azure AD, dispositivos windows 10, tome as seguintes etapas:

      1. Abra o pedido de comando como administrador
      1. Introduza `dsregcmd /forcerecovery` (Nota: Tem de ser administrador para realizar esta ação).
      1. Clique em "Iniciar sessão" no diálogo que se abre e continue com o sinal em processo.
      1. Assine e assine de volta no dispositivo para completar a recuperação.

      Para dispositivos Windows 10 registados pela Azure AD, tome as seguintes etapas:

      1. Vá a **Definições** > Contas > Trabalho de **Acesso ou Escola.** 
      1. Selecione a conta e selecione **Desligar**.
      1. Clique em "+ Connect" e registe o dispositivo novamente através do sinal em processo.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>P: Por que vejo entradas duplicadas de dispositivos no portal Azure?

**R:**

- No caso do Windows 10 e do Windows Server 2016, as tentativas repetidas de se desintegrarem e voltarem a juntar-se ao mesmo dispositivo podem causar entradas duplicadas. 
- Cada utilizador do Windows que utiliza **add work ou conta escolar** cria um novo registo de dispositivocom o mesmo nome de dispositivo.
- Para versões de baixo nível do Windows OS que estão no local o domínio do Diretório Azure, o registo automático cria um novo registo de dispositivos com o mesmo nome de dispositivo para cada utilizador de domínio que insere no dispositivo. 
- Uma máquina azure ad que é limpa, reinstalada e reunida com o mesmo nome aparece como outro disco com o mesmo nome de dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>P: O registo do dispositivo Windows 10 no Azure AD suporta TPMs no modo FIPS?

**A:** O registo do dispositivo Windows 10 apenas suportado por TPM 2.0 compatível com FIPS e não suportado para TPM 1.2. Se os seus dispositivos tiverem TPM 1.2 compatível com FIPS, deve desativá-los antes de prosseguir com a adesão do Azure AD ou a adesão do Hybrid Azure AD. A Microsoft não fornece nenhuma ferramenta para desativar o modo FIPS para TPMs, uma vez que está dependente do fabricante tpm. Contacte o seu hardware OEM para obter suporte. 

---

**P: Porque é que um utilizador ainda pode aceder a recursos a partir de um dispositivo que desabilitei no portal Azure?**

**A:** Demora até uma hora para que seja aplicada uma revogação a partir do momento em que o dispositivo Azure AD é marcado como desativado.

>[!NOTE] 
>Para dispositivos matriculados, recomendamos que limpe o dispositivo para garantir que os utilizadores não podem aceder aos recursos. Para mais informações, consulte [o que é a inscrição do dispositivo?](/intune/deploy-use/enroll-devices-in-microsoft-intune) 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>P: Por que existem dispositivos marcados como "Pendente" sob a coluna REGISTADA no portal Azure?

**R:** Pendente indica que o dispositivo não está registado. Este estado indica que um dispositivo foi sincronizado utilizando a ligação Azure AD a partir de um AD no local e está pronto para o registo do dispositivo. Estes dispositivos têm o conjunto DE TIPO JOIN para "Hybrid Azure AD juntou-se". Saiba mais sobre [como planear a sua implementação híbrida Azure Ative Directory](hybrid-azuread-join-plan.md).

>[!NOTE]
>Um dispositivo também pode mudar de ter um estado registado para "Pendente"
>* Se um dispositivo for eliminado primeiro do Azure AD e resincronizado a partir de um AD no local.
>* Se um dispositivo for removido de um âmbito de sincronização no Azure AD Connect e adicionado de volta.
>
>Em ambos os casos, deve voltar a registar o dispositivo manualmente em cada um destes dispositivos. Para analisar se o dispositivo foi previamente registado, pode [resolver problemas utilizando o comando dsregcmd](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Azure AD junta-se às FAQ

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>P: Como posso desafiliar um dispositivo Azure AD localmente no dispositivo?

**A:** Para dispositivos adatos azure puros, certifique-se de que tem uma conta de administrador local offline ou crie uma. Não pode iniciar sessão com credenciais de utilizador da Azure AD. Em seguida, vá a **Definições** > **Contas** > Trabalho de **Acesso ou Escola.** Selecione a sua conta e selecione **Desligar**. Siga as instruções e forneça as credenciais do administrador local quando solicitada. Reinicie o dispositivo para terminar o processo de desafiliação.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>P: O acesso dos meus utilizadores ao Azure AD pode aderir a dispositivos que são eliminados ou desativados em Azure AD?

**R:** Sim. O Windows tem um nome de utilizador e uma capacidade de senha em cache que permite aos utilizadores que assinaram anteriormente aceder ao ambiente de trabalho rapidamente mesmo sem conectividade de rede. 

Quando um dispositivo é apagado ou desativado em Azure AD, não é do conhecimento do dispositivo Windows. Assim, os utilizadores que assinaram anteriormente continuam a aceder ao ambiente de trabalho com o nome de utilizador e a palavra-passe em cache. Mas como o dispositivo é eliminado ou desativado, os utilizadores não podem aceder a quaisquer recursos protegidos pelo Acesso Condicional baseado no dispositivo. 

Os utilizadores que não assinaram anteriormente não conseguem aceder ao dispositivo. Não há nenhum nome de utilizador e senha em cache para eles. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>P: Pode um utilizador desativado ou eliminado iniciar sessão num Azure AD

**A:** Sim, mas só por um tempo limitado. Quando um utilizador é eliminado ou desativado em Azure AD, não é imediatamente conhecido do dispositivo Windows. Assim, os utilizadores que assinaram anteriormente podem aceder ao ambiente de trabalho com o nome de utilizador e senha em cache. 

Normalmente, o dispositivo está ciente do estado do utilizador em menos de quatro horas. Em seguida, o Windows bloqueia o acesso desses utilizadores ao ambiente de trabalho. Uma vez que o utilizador é eliminado ou desativado em Azure AD, todos os seus tokens são revogados. Para que não possam aceder a recursos. 

Os utilizadores apagados ou desativados que não tenham assinado anteriormente não podem aceder a um dispositivo. Não há nenhum nome de utilizador e senha em cache para eles. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>P: Porque é que os meus utilizadores têm problemas com o Azure AD que se juntou aos dispositivos depois de alterar em upn?

**A:** Atualmente, as alterações da UPN não são totalmente suportadas em dispositivos ligados ao Azure AD. Assim, a sua autenticação com a AD Azure falha após as alterações da UPN. Como resultado, os utilizadores têm problemas de SSO e Acesso Condicional nos seus dispositivos. Neste momento, os utilizadores precisam de iniciar sessão no Windows através do azulejo "Outro utilizador", utilizando a sua nova UPN para resolver este problema. Estamos neste momento a trabalhar na abordagem desta questão. No entanto, os utilizadores que se inscrevam no Windows Hello for Business não enfrentam este problema. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>P: Os meus utilizadores não podem pesquisar impressoras a partir de dispositivos azure AD. Como posso ativar a impressão a partir desses dispositivos?

**A:** Para implementar impressoras para dispositivos ligados ao Azure AD, consulte [a implementação da Cloud Print híbrida do Windows Server com pré-autenticação](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Precisa de um Windows Server no local para implementar a impressão de nuvem híbrida. Atualmente, o serviço de impressão baseado em nuvem não está disponível. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>P: Como posso ligar-me a um dispositivo de ad ad ida e volta do Azure remoto?

**A:** Consulte A Connect para pc [de diretório ativo azure remoto](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>P: Porque é que os meus utilizadores vêem *que não consegues chegar lá a partir daqui?*

**A:** Configurou certas regras de Acesso Condicional para exigir um estado específico do dispositivo? Se o dispositivo não cumprir os critérios, os utilizadores estão bloqueados e vêem essa mensagem. Avaliar as regras da política de Acesso Condicional. Certifique-se de que o aparelho satisfaz os critérios para evitar a mensagem.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>P: Porque é que alguns dos meus utilizadores não recebem pedidos de autenticação multi-factor Azure em dispositivos ligados ao Azure AD?

**A:** Um utilizador pode juntar-se ou registar um dispositivo com AD Azure utilizando a Autenticação Multi-Factor. Em seguida, o próprio dispositivo torna-se um segundo fator de confiança para esse utilizador. Sempre que o mesmo utilizador acede ao dispositivo e acede a uma aplicação, a Azure AD considera o dispositivo como um segundo fator. Permite que o utilizador aceda sem problemas às aplicações sem avisos adicionais de autenticação multi-factor. 

Este comportamento:

- É aplicável ao Azure AD e dispositivos registados pela Azure AD - mas não para dispositivos híbridos Azure AD.
- Não é aplicável a qualquer outro utilizador que inste o mesmo dispositivo. Assim, todos os outros utilizadores que acedem a esse dispositivo recebem um desafio de autenticação multi-factor. Depois podem aceder a aplicações que requerem autenticação multi-factor.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>P: Por que recebo um *nome de utilizador ou palavra-passe é uma* mensagem incorreta para um dispositivo a que acabei de aderir ao Azure AD?

**A:** As razões comuns para este cenário são as seguintes:

- As suas credenciais de utilizador já não são válidas.
- O seu computador não consegue comunicar com o Diretório Ativo Azure. Verifique se há problemas de conectividade da rede.
- Os inscrições federados requerem que o seu servidor da federação suporte os pontos finais wS-Trust que estão ativados e acessíveis. 
- Permitiu a autenticação pass-through. Por isso, a sua senha temporária precisa de ser alterada quando se inscreveu.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>P: Por que vejo os *Oops... ocorreu um erro!* dialogar quando tento azure AD juntar-se ao meu PC?

**A:** Este erro ocorre quando configura a inscrição do Diretório Ativo Azure com a Intune. Certifique-se de que o utilizador que tentar aderir ao Azure AD tem a licença Intune correta atribuída. Para mais informações, consulte [Configurar a inscrição para dispositivos Windows](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>P: Porque é que a minha tentativa de adotei a Azure AD falhou, embora não tenha conseguido nenhuma informação de erro?

**A:** Uma causa provável é que tenha sintetizado no dispositivo usando a conta de administrador incorporado local. Crie uma conta local diferente antes de utilizar o Azure Ative Directory para terminar a configuração. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>P:Quais são os certificados de acesso MS-Organização-P2P-presentes nos nossos dispositivos Windows 10?

**A:** Os certificados MS-Organization-P2P-Access são emitidos pela Azure AD para ambos, Azure AD juntou-se e híbrido Azure AD aderiu a dispositivos. Estes certificados são utilizados para permitir a confiança entre dispositivos no mesmo inquilino para cenários remotos de ambiente de trabalho. Um certificado é emitido para o dispositivo e outro é emitido para o utilizador. O certificado do dispositivo está presente em `Local Computer\Personal\Certificates` e é válido por um dia. Este certificado é renovado (através da emissão de um novo certificado) se o dispositivo ainda estiver ativo em Azure AD. O certificado de utilizador encontra-se presente em `Current User\Personal\Certificates` e este certificado também é válido por um dia, mas é emitido a pedido quando um utilizador tenta uma sessão remota de desktop para outro dispositivo azure AD. Não é renovado no prazo de validade. Ambos os certificados são emitidos utilizando o certificado de acesso MS-Organização-P2P-presente na `Local Computer\AAD Token Issuer\Certificates`. Este certificado é emitido pela Azure AD durante o registo do dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>P:Por que vejo vários certificados expirados emitidos pelo MS-Organization-P2P-Access nos nossos dispositivos Windows 10? Como posso apagá-los?

**A:** Houve um problema identificado na versão 1709 do Windows 10 e inferior onde os certificados de Acesso MS-Organização-P2P-P2P expirados continuaram a existir na loja de computadores devido a problemas criptográficos. Os seus utilizadores podem enfrentar problemas com a conectividade da rede, se estiver a utilizar clientes VPN (por exemplo, Cisco AnyConnect) que não consigam lidar com o grande número de certificados expirados. Este problema foi corrigido na versão do Windows 10 1803 para eliminar automaticamente quaisquer certificados de Acesso MS-Organização-P2P expirados. Pode resolver este problema atualizando os seus dispositivos para o Windows 10 1803. Se não conseguir atualizar, pode eliminar estes certificados sem qualquer impacto adverso.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Hybrid Azure AD junta-se às FAQ

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>P: Como posso desafiliar um dispositivo Híbrido Azure ad localmente no dispositivo?

**A:** Para dispositivos híbridos Azure AD, certifique-se de desligar o registo automático. Em seguida, a tarefa programada não volta a registar o dispositivo. Em seguida, abra um pedido de comando como administrador e introduza `dsregcmd.exe /debug /leave`. Ou executar este comando como um script em vários dispositivos para não se juntar a granel.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>P: Onde posso encontrar informações de resolução de problemas para diagnosticar a AD Híbrida Azure juntar falhas?

**A:** Para obter informações sobre resolução de problemas, consulte estes artigos:

- [O Diretório Ativo Azure Ative de Resolução de Problemas juntou-se aos dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Resolução de problemas híbrido Azure Ative Directory juntou-se a dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>P: Por que vejo um registo registado em Azure AD duplicado para o meu Dispositivo Azure AD híbrido Windows 10 na lista de dispositivos AD Azure?

**A:** Quando os seus utilizadores adicionam as suas contas a apps num dispositivo unido ao domínio, podem ser solicitadas com **a conta Add para o Windows?** Se introduzirem **Sim** no aviso, o dispositivo regista-se com a AD Azure. O tipo de confiança está marcado como Azure AD registado. Depois de permitir a adesão híbrida azure a d.ida à sua organização, o dispositivo também recebe a adesão híbrida Azure AD. Em seguida, dois estados de dispositivo aparecem para o mesmo dispositivo. 

A adesão da Hybrid Azure AD tem precedência sobre o estado registado pela Azure AD. Assim, o seu dispositivo é considerado híbrido Azure AD aderiu a qualquer autenticação e avaliação de Acesso Condicional. Pode eliminar com segurança o registo do dispositivo Azure AD do portal Azure AD. Aprenda a [evitar ou limpar este duplo estado na máquina do Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>P: Porque é que os meus utilizadores têm problemas no Windows 10 hybrid Azure AD juntou-se a dispositivos depois de alterar a sua UPN?

**A:** Atualmente, as alterações da UPN não são totalmente suportadas com dispositivos híbridos Azure AD. Enquanto os utilizadores podem iniciar sessão no dispositivo e aceder às suas aplicações no local, a autenticação com a AD Azure falha após uma alteração da UPN. Como resultado, os utilizadores têm problemas de SSO e Acesso Condicional nos seus dispositivos. Neste momento, é necessário desatar o dispositivo a partir de Azure AD (executar "dsregcmd /leave" com privilégios elevados) e voltar a juntar-se (acontece automaticamente) para resolver o problema. Estamos neste momento a trabalhar na abordagem desta questão. No entanto, os utilizadores que se inscrevam no Windows Hello for Business não enfrentam este problema. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>P: O Windows 10 híbrido Azure AD junta-se a dispositivos que requerem uma linha de visão para o controlador de domínio para ter acesso aos recursos da nuvem?

**A:** Não, exceto quando a palavra-passe do utilizador é alterada. Após a adesão híbrida do Windows 10, a adesão ao Azure AD híbrido está concluída e o utilizador assinou pelo menos uma vez, o dispositivo não necessita de linha de visão para o controlador de domínio para aceder aos recursos da cloud. O Windows 10 pode obter uma única entrada nas aplicações DaD Azure a partir de qualquer lugar com uma ligação à Internet, exceto quando uma palavra-passe é alterada. Os utilizadores que acedem ao Windows Hello for Business continuam a receber um único sinal de acesso às aplicações da Azure AD mesmo depois de uma alteração de palavra-passe, mesmo que não tenham uma linha de visão para o seu controlador de domínio. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>P: O que acontece se um utilizador alterar a sua palavra-passe e tentar iniciar sessão no seu Dispositivo Azure Azure híbrido Windows 10, fora da rede corporativa?

**A:** Se uma palavra-passe for alterada fora da rede corporativa (por exemplo, utilizando o Azure AD SSPR), o utilizador iniciará o seu início com a nova palavra-passe. Para dispositivos híbridos Azure AD, o Ative Directory é a principal autoridade. Quando um dispositivo não tem linha de visão para o controlador de domínio, não é possível validar a nova palavra-passe. Assim, o utilizador precisa estabelecer a ligação com o controlador de domínio (seja via VPN ou estar na rede corporativa) antes de poder iniciar sessão no dispositivo com a sua nova palavra-passe. Caso contrário, só podem iniciar sessão com a sua antiga senha devido ao sinal em cache no Windows. No entanto, a antiga senha é invalidada pela Azure AD durante pedidos simbólicos e, portanto, impede uma única inscrição e falha quaisquer políticas de Acesso Condicional baseadas no dispositivo. Este problema não ocorre se utilizar o Windows Hello for Business. 

---

## <a name="azure-ad-register-faq"></a>Registo da AD Azure FAQ

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>P: Como remover um estado registado em Azure AD para um dispositivo localmente?

**R:** 
- Para dispositivos registados pela AD Do Windows 10, aceda a **Definições** > **Contas** > Trabalho de **Acesso ou Escola**. Selecione a sua conta e selecione **Desligar**. O registo do dispositivo é por perfil de utilizador no Windows 10.
- Para iOS e Android, pode utilizar as **definições** da aplicação Autenticadora Microsoft > **Registo do Dispositivo** e selecionar dispositivo **Desregistar**.
- Para o macOS, pode utilizar a aplicação Microsoft Intune Company Portal para desinscrever o dispositivo da gestão e remover qualquer registo. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>P: Como posso impedir os utilizadores de adicionar em contades de trabalho adicionais (Azure AD registado) nos meus dispositivos corporativos windows 10?

**A:** Ative o seguinte registo para impedir que os seus utilizadores adicionem contas de trabalho adicionais ao seu domínio corporativo, a Azure AD juntou-se ou o Azure AD híbrido juntou-se a dispositivos Windows 10. Esta política também pode ser usada para bloquear máquinas unidas de domínio de inadvertidamente ter a Azure AD registada com a mesma conta de utilizador. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>P: Posso registar dispositivos Android ou iOS BYOD?

**A:** Sim, mas apenas com o serviço de registo de dispositivos Azure e para clientes híbridos. Não é suportado com o serviço de registo de dispositivos no local em Serviços da Federação de Diretórios Ativos (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>P: Como posso registar um dispositivo macOS?

**A:** Tome os seguintes passos:

1.  [Criar uma política de conformidade](/intune/compliance-policy-create-mac-os)
1.  [Defina uma política de acesso condicional para dispositivos macOS](../active-directory-conditional-access-azure-portal.md) 

**Observações:**

- Os utilizadores incluídos na sua política de Acesso Condicional precisam de uma [versão suportada do Office para](../conditional-access/concept-conditional-access-conditions.md) o macOS aceder aos recursos. 
- Durante a primeira tentativa de acesso, os seus utilizadores são solicitados a inscrever o dispositivo utilizando o portal da empresa.

---
## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [dispositivos registados da Azure AD](concept-azure-ad-register.md)
- Saiba mais sobre [dispositivos azure AD](concept-azure-ad-join.md)
- Saiba mais sobre [dispositivos híbridos Azure AD](concept-azure-ad-join-hybrid.md)
