---
title: Gestão de dispositivos do Azure Active Directory perguntas frequentes | Documentos da Microsoft
description: Gestão de dispositivos do Azure Active Directory FAQ.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e29c58c0e9a31b2eb3e3d7e237a3db8173214faf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110653"
---
# <a name="azure-active-directory-device-management-faq"></a>Gestão de dispositivos do Azure Active Directory FAQ

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>P. Recentemente eu registado o dispositivo. Por que motivo não vejo o dispositivo em minhas informações de utilizador no portal do Azure? Ou, por que é o proprietário do dispositivo marcado como dispositivos associados ao n/d para híbrida do Azure Active Directory (Azure AD)?

**R:** Dispositivos Windows 10 que estão associados ao Azure AD híbrido não apresentados no **dispositivos dos utilizadores**.
Utilize o **todos os dispositivos** vista no portal do Azure. Também pode utilizar o PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet.

Apenas os seguintes dispositivos estão listados na **dispositivos dos utilizadores**:

- Todos os dispositivos pessoais que não são do Azure AD híbrido associado. 
- Todos os não - Windows 10 ou dispositivos do Windows Server 2016.
- Todos os dispositivos não Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>P. Como posso saber o que é o estado de registo de dispositivo do cliente?

**R:** No portal do Azure, aceda a **todos os dispositivos**. Procure o dispositivo com o ID de dispositivo. Verifique o valor na coluna de tipo de associação. Às vezes, o dispositivo poderá repor ou recriado. Portanto, é essencial para também verificar o estado de registo do dispositivo no dispositivo:

- Para Windows 10 e Windows Server 2016 ou posterior inscritos, execute `dsregcmd.exe /status`.
- Para versões de SO de nível inferior, executar `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>P. Posso ver o registo de dispositivo sob as informações de utilizador no portal do Azure. E posso ver o estado como registrado no dispositivo. Estou configuro corretamente para utilizar o acesso condicional?

**R:** O estado de associação de dispositivo, apresentado por **deviceID**, tem correspondência com o estado do Azure AD e satisfazer quaisquer critérios de avaliação para o acesso condicional. Para obter mais informações, consulte [exigir dispositivos para aceder à aplicação de cloud com o acesso condicional geridos](../conditional-access/require-managed-devices.md).

---

### <a name="q-i-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered"></a>P. Posso eliminar o meu dispositivo no portal do Azure ou com o Windows PowerShell. Mas o estado local no dispositivo diz que permanece registado.

**R:** Esta operação é propositado. O dispositivo não tem acesso a recursos na cloud. 

Se pretender voltar a registar, tem de executar uma ação manual no dispositivo. 

Para limpar o estado de associação do Windows 10 e Windows Server 2016 que ingressaram no domínio do Active Directory no local, siga os passos seguintes:

1.  Abra a linha de comandos como administrador.

2.  Introduza `dsregcmd.exe /debug /leave`.

3.  Terminar sessão e inicie sessão acionar a tarefa agendada que regista o dispositivo novamente com o Azure AD. 

Para versões de SO de Windows de nível inferior que estão associados a um domínio de Active Directory no local, siga os passos seguintes:

1.  Abra a linha de comandos como administrador.
2.  Introduza `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Introduza `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>P. Por que vejo as entradas de dispositivo duplicado no portal do Azure?

**R:**

-   Para Windows 10 e Windows Server 2016, as tentativas repetidas a anulação da associação ao e voltar a participar no mesmo dispositivo poderão causar entradas duplicadas. 

-   Cada utilizador do Windows que utiliza **conta escolar ou profissional adicionar** cria um novo registo de dispositivo com o mesmo nome de dispositivo.

-   Para versões de SO de Windows de nível inferior que estão associados a um domínio de diretório do Azure no local, o registo automático cria um novo registo de dispositivo com o mesmo nome de dispositivo para cada utilizador de domínio que inicia sessão no dispositivo. 

-   Um computador associado do Azure AD, que foi apagado, reinstalado e reagregado com o mesmo nome exibido como outro registo com o mesmo nome de dispositivo.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>P. O registo de dispositivos Windows 10 no Azure AD suporta TPMs no modo FIPS?

**R:** Não, atualmente registo de dispositivos no Windows 10 para todos os Estados de dispositivo - associação ao Azure AD híbrido, associação do Azure AD e o Azure AD registado - não suporta TPMs no modo FIPS. Com êxito, Junte-se ou se registrar para o Azure AD, tem de ser desativado para os TPMs nesses dispositivos modo FIPS

---

**P: Por que pode um utilizador ainda aceder aos recursos de um dispositivo, desativada no portal do Azure?**

**R:** Demoram até uma hora para um revogar a ser aplicado.

>[!NOTE] 
>Para dispositivos inscritos, recomendamos que apaga o dispositivo para se certificar de que os utilizadores não podem aceder os recursos. Para obter mais informações, consulte [o que é a inscrição de dispositivos?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Associação do Azure AD, perguntas frequentes

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>P. Como, desassocie um dispositivo associado ao Azure AD localmente no dispositivo?

**R:** 
- Para o Azure AD híbrido associado ao dispositivos, certifique-se desativar o registo automático. Em seguida, a tarefa agendada não volte a registar o dispositivo. Em seguida, abra uma linha de comandos como administrador e introduza `dsregcmd.exe /debug /leave`. Ou execute este comando como um script em vários dispositivos para anulação da associação em massa.

- Para puro do Azure AD dispositivos associados, certifique-se de que tem uma conta de administrador local offline ou criar um. Não é possível iniciar sessão com qualquer credenciais de utilizador do Azure AD. Em seguida, aceda a **configurações** > **contas** > **acesso profissional ou escolar**. Selecione a sua conta e selecione **desligar**. Siga as instruções e forneça as credenciais de administrador local quando lhe for pedido. Reinicie o dispositivo para concluir o processo de unjoin.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>P. Podem os meus utilizadores iniciar sessão em dispositivos associados ao Azure AD que são excluídos ou desativados no Azure AD?

**R:** Sim. Windows tem um nome de utilizador em cache e a capacidade de palavra-passe que permite aos utilizadores que se inscreveram no anteriormente para acessar a área de trabalho rapidamente, mesmo sem conectividade de rede. 

Quando um dispositivo é eliminado ou desativado no Azure AD, não é conhecido no dispositivo do Windows. Para que os utilizadores que iniciou sessão anteriormente continuam a aceder o ambiente de trabalho com o nome de utilizador em cache e a palavra-passe. Mas, à medida que o dispositivo é eliminado ou desativado, os utilizadores não podem aceder a quaisquer recursos protegidos pelo acesso condicional com base no dispositivo. 

Os utilizadores que não iniciem sessão anteriormente não é possível aceder ao dispositivo. Não existe nenhum nome de utilizador em cache e a palavra-passe ativada para os mesmos. 

---

### <a name="q-can-disabled-or-deleted-users-sign-in-to-azure-ad-joined-devices"></a>P. Podem desativados ou eliminados utilizadores iniciar sessão em dispositivos associados ao Azure AD?

**R:** Sim, mas apenas por um período limitado. Quando um utilizador é eliminado ou desativado no Azure AD, não imediatamente é conhecido no dispositivo do Windows. Portanto, os utilizadores que iniciou sessão anteriormente podem acessar a área de trabalho com o nome de utilizador em cache e a palavra-passe. 

Normalmente, o dispositivo está ciente do Estado de utilizador em menos de quatro horas. Em seguida, Windows bloqueia o acesso desses usuários para a área de trabalho. À medida que o utilizador é eliminado ou desativado no Azure AD, todos os seus tokens são revogados. Portanto, eles não é possível aceder a quaisquer recursos. 

Os utilizadores desativados ou eliminados que não iniciem sessão anteriormente não é possível aceder a dispositivos. Não existe nenhum nome de utilizador em cache e a palavra-passe ativada para os mesmos. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>P. Por que meus usuários tiverem problemas em dispositivos associados ao Azure AD depois de alterar o respetivo UPN?

**R:** Atualmente, as alterações UPN totalmente não são suportadas em dispositivos associados ao Azure AD. Portanto, a autenticação com o Azure AD falha depois das alterações UPN. Como resultado, os utilizadores têm o SSO e problemas de acesso condicional nos respetivos dispositivos. Neste momento, os utilizadores têm de iniciar sessão no Windows através do mosaico "Outro utilizador" com o respetivo UPN novo para resolver este problema. Estamos atualmente a trabalhar sobre como solucionar este problema. No entanto, os utilizadores iniciar sessão com o Windows Hello para empresas se deparam este problema. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>P. Os meus utilizadores não consegue pesquisar impressoras a partir de dispositivos associados ao Azure AD. Como posso ativar a impressão a partir desses dispositivos?

**R:** Para implantar impressoras para o Azure AD associado dispositivos, consulte [implementar o Windows Server Hybrid Cloud Print com pré-autenticação](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). É necessário um servidor de Windows no local para implementar a impressão de cloud híbrida. Atualmente, serviço de impressão baseado na nuvem não está disponível. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>P. Como posso ligar a um remoto do Azure AD associado ao dispositivo?

**R:** Ver [ligue-se ao PC remoto associado ao Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>P. Por que os meus utilizadores veem *não pode aceder aí a partir daqui*?

**R:** Tiver configurado determinadas regras de acesso condicional para requerer um Estado de dispositivo específico? Se o dispositivo não cumpre os critérios, os utilizadores são bloqueados e Verão que a mensagem. Avalie as regras de política de acesso condicional. Certifique-se de que o dispositivo cumpre os critérios para evitar a mensagem.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>P. Por que alguns dos meus utilizadores não para obter instruções de multi-factor Authentication em dispositivos associados ao Azure AD?

**R:** Um utilizador pode associar ou registar um dispositivo com o Azure AD utilizando o multi-factor Authentication. Em seguida, o próprio dispositivo torna-se um segundo fator fidedigno para esse utilizador. Sempre que o mesmo utilizador inicia sessão no dispositivo e acede a uma aplicação, do Azure AD considera o dispositivo como um segundo fator. Ele permite que o utilizador acessar diretamente os aplicativos sem pedidos adicionais do multi-factor Authentication. 

Esse comportamento:

- É aplicável para o Azure AD associado e do Azure AD registado dispositivos - mas não para híbrida do Azure AD associado dispositivos.

- Não é aplicável a qualquer outro utilizador que inicia sessão nesse dispositivo. Portanto, todos os outros utilizadores que acedam a esse dispositivo obtém um desafio de multi-factor Authentication. Em seguida, podem aceder a aplicações que necessitam de multi-factor Authentication.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>P. Por que motivo recebo uma *nome de utilizador ou palavra-passe está incorreta* mensagem para um dispositivo, eu apenas associados ao Azure AD?

**R:** Seguem-se motivos comuns para este cenário:

- As suas credenciais de utilizador já não são válidos.

- O computador não consegue comunicar com o Azure Active Directory. Verificar a existência de quaisquer problemas de conectividade de rede.

- Inícios de sessão federados requerem o seu servidor de Federação para suportar pontos de extremidade do WS-Trust que estão ativadas e acessível. 

- Ativou a autenticação pass-through. Portanto, a palavra-passe temporária precisa ser alterada quando iniciar sessão.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>P. Por que razão vejo o *UPS... Ocorreu um erro!* caixa de diálogo quando tento para o Azure AD associar o meu PC?

**R:** Este erro acontece quando configurar a inscrição do Azure Active Directory com o Intune. Certifique-se de que o utilizador que tenta a associação do Azure AD tem a licença correta do Intune atribuída. Para obter mais informações, consulte [configurar a inscrição para dispositivos Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>P. Por que minha tentativa para o Azure AD associar falham um PC, embora eu não recebeu qualquer informação de erro?

**R:** Uma causa provável é que iniciou sessão no dispositivo com a conta interna de administrador local. Crie uma conta local diferente antes de utilizar a associação do Azure Active Directory para concluir a configuração. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>P: quais são os certificados de MS-organização-P2P-acesso presentes em nossos dispositivos Windows 10?

**R:** Os MS-organização-P2P-acesso os certificados são emitidos pelo Azure AD para ambos, associado ao Azure AD e dispositivos associados ao Azure AD híbrido. Estes certificados são utilizados para ativar a fidedignidade entre os dispositivos no mesmo inquilino para cenários de área de trabalho remotos. Um certificado é emitido para o dispositivo e o outro é emitido para o usuário. O certificado do dispositivo está presente no `Local Computer\Personal\Certificates` e é válido durante um dia. Este certificado é renovado (ao emitir um novo certificado) se o dispositivo ainda está ativo no Azure AD. O certificado de utilizador está presente no `Current User\Personal\Certificates` e este certificado também é válido durante um dia, mas ele é emitido por demanda, quando um utilizador tenta uma sessão de área de trabalho remota para outro dispositivo de associado ao Azure AD. Não será renovado no expiração. Ambos estes certificados são emitidos com o certificado de MS-organização-P2P-acesso presente no `Local Computer\AAD Token Issuer\Certificates`. Este certificado é emitido pelo Azure AD durante o registo do dispositivo. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why vejo vários certificados expirados emitidos pela MS-organização-P2P-acesso nos nossos dispositivos Windows 10? Como posso exclui-los?

**R:** Ocorreu um problema identificado no Windows 10 versão 1709 ou inferior, onde certificados expirados do MS-organização-P2P-acesso continuaram a existir no arquivo do computador devido a problemas de criptografia. Os utilizadores podem aparecer problemas com a conectividade de rede, se estiver a utilizar quaisquer clientes VPN (por exemplo, o Cisco AnyConnect) que não é possível processar o elevado número de certificados expirados. Este problema foi corrigido na versão do Windows 10 1803 eliminar automaticamente esses certificados MS-organização-P2P-acesso expirados. Pode resolver este problema atualizando seus dispositivos Windows 10 1803. Se não é possível atualizar, pode eliminar estes certificados sem qualquer impacto negativo.  

---


## <a name="hybrid-azure-ad-join-faq"></a>Perguntas frequentes sobre a associação do Azure AD híbrido

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>P. Onde posso encontrar a resolução de problemas de informações para diagnosticar falhas de associação do Azure AD híbrido?

**R:** Para informações de resolução de problemas, veja estes artigos:

- [Resolução de problemas híbrida do Azure Active Directory associados a dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)

- [Resolução de problemas híbrida do Azure Active Directory dispositivos associados a um nível inferior](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>P. Por que razão vejo um duplicado do Azure AD registado registo para meu Azure AD híbrido de Windows 10 associados a um dispositivo na lista de dispositivos do Azure AD?

**R:** Quando os utilizadores adicionar suas contas para aplicações num dispositivo associado a um domínio, poderá ser pedidos com **adicionar a conta ao Windows?** Se ele inserir **Sim** na linha de comandos, o dispositivo se registra com o Azure AD. O tipo de confiança é marcado como o Azure AD registado. Depois de ativar a associação ao Azure AD híbrido na sua organização, o dispositivo obtém também associado ao Azure AD híbrido. Em seguida, dois Estados de dispositivo apresentados para o mesmo dispositivo. 

Associação ao Azure AD híbrido tem precedência sobre o estado do Azure AD registado. Portanto, o dispositivo é considerado associados para qualquer autenticação e a avaliação de acesso condicional do Azure AD híbrido. Pode eliminar em segurança ao registo do dispositivo do Azure AD registado a partir do portal do Azure AD. Aprenda a [evitar ou limpar este estado duplo na máquina do Windows 10](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 


---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>P. Por que meus usuários tiverem problemas em dispositivos do Windows 10 híbridos associados ao Azure AD depois de alterar o respetivo UPN?

**R:** Atualmente as alterações do UPN não são totalmente suportadas com os dispositivos associados ao Azure AD híbrido. Embora os utilizadores podem iniciar sessão no dispositivo e acesso às suas aplicações no local, a autenticação com o Azure AD falha depois de alterar um UPN. Como resultado, os utilizadores têm o SSO e problemas de acesso condicional nos respetivos dispositivos. Neste momento, precisa desassocie o dispositivo do Azure AD (execute "dsregcmd /leave" com privilégios elevados) e voltar a associar (ocorre automaticamente) para resolver o problema. Estamos atualmente a trabalhar sobre como solucionar este problema. No entanto, os utilizadores iniciar sessão com o Windows Hello para empresas se deparam este problema. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>P. Dispositivos do Windows 10 híbridos associados ao Azure AD precisam linha Visual para o controlador de domínio para obter acesso a recursos na cloud?

**R:** Em geral, não, exceto quando a senha do usuário for alterada. Associação do após Windows 10 híbrida do Azure AD estiver concluída e o utilizador ter iniciado sessão pelo menos uma vez, o dispositivo não exige a linha de visão para o controlador de domínio para aceder a recursos na cloud. Windows 10 pode obter único início de sessão para aplicações do Azure AD em qualquer lugar com uma ligação à internet, exceto quando uma palavra-passe for alterada. Os utilizadores que inicie sessão com Windows Hello para empresas continua a receber único inicie sessão aplicações do Azure AD, mesmo depois de alterar uma palavra-passe, mesmo que eles não têm de linha de visão para seu controlador de domínio. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>P. O que acontece se um utilizador altera a palavra-passe e tentar iniciar sessão para o Azure AD híbrido de Windows 10 associados a um dispositivo fora da rede empresarial?

**R:** Se uma palavra-passe é alterada fora da rede empresarial (por exemplo, utilizando o Azure AD SSPR), em seguida, irá falhar o início de sessão do utilizador com a nova palavra-passe. Para dispositivos de associados ao Azure AD híbrido, o Active Directory no local é a autoridade primária. Quando um dispositivo não tem uma linha Visual para o controlador de domínio, é não é possível validar a palavra-passe nova. Então, o utilizador tem de estabelecer ligação com o controlador de domínio (quer através de VPN ou a ser na rede empresarial) antes que sejam capazes de iniciar sessão no dispositivo com a nova palavra-passe. Caso contrário, eles podem apenas iniciar sessão com a palavra-passe antiga devido a capacidade de início de sessão em cache no Windows. No entanto, a palavra-passe antiga é invalidada pelo Azure AD durante pedidos de token e assim, impede que o início de sessão único em e realiza a quaisquer políticas de acesso condicional com base no dispositivo. Este problema não ocorre se utilizar o Windows Hello para empresas. 

---


## <a name="azure-ad-register-faq"></a>FAQ de Registro do Azure AD

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>P. Pode registar dispositivos BYOD de Android ou iOS?

**R:** Sim, mas apenas com o serviço de registo de dispositivos no Azure e para clientes híbridos. Não é suportada com o serviço de registo de dispositivos no local nos serviços de Federação do Active Directory (AD FS).

### <a name="q-how-can-i-register-a-macos-device"></a>P. Como posso registrar um dispositivo macOS?

**R:** Siga os passos seguintes:

1.  [Criar uma política de conformidade](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definir uma política de acesso condicional para dispositivos macOS](../active-directory-conditional-access-azure-portal.md) 

**Observações:**

- Os utilizadores incluídos na sua necessidade de política de acesso condicional uma [uma versão suportada do Office para macOS](../conditional-access/technical-reference.md#client-apps-condition) para aceder aos recursos. 

- Durante a primeira tentativa de acesso, os utilizadores são-lhe pedidos para inscrever o dispositivo com o portal da empresa.

