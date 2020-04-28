---
title: Plan hybrid Azure Ative Directory join - Azure Ative Directory
description: Saiba como configurar dispositivos associados ao Azure Active Directory híbrido.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22ab3e7403069ed1b579631b88c2ac2c41191ecd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181329"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Como: Planeie a sua direção ativa híbrida Azure aderir à implementação

De forma semelhante a um utilizador, um dispositivo é outra identidade central que pretende proteger e usá-lo para proteger os seus recursos a qualquer momento e de qualquer local. Pode atingir este objetivo trazendo e gerindo identidades de dispositivos em Azure AD utilizando um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Ao trazer os seus dispositivos para o Azure AD, maximiza a produtividade dos seus utilizadores através do início de sessão único (SSO) nos seus recursos na cloud e no local. Ao mesmo tempo, pode garantir o acesso à sua nuvem e recursos no local com [Acesso Condicional](../active-directory-conditional-access-azure-portal.md).

Se tiver um ambiente de Diretório Ativo (AD) no local e quiser juntar-se aos seus computadores ad-join-by-ad para Azure AD, pode fazê-lo fazendo a adesão híbrida azure AD. Este artigo fornece-lhe os passos relacionados para implementar um Azure AD híbrido a desafiliar-se no seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que está familiarizado com a Introdução à Gestão de Identidade do [Dispositivo no Diretório Ativo do Azure](../device-management-introduction.md).

> [!NOTE]
> A versão mínima necessária para o controlador de domínio necessário para a adesão ao Windows 10 híbrido Azure AD é o Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planeie a sua implementação

Para planear a sua implementação híbrida da AD Azure, deve familiarizar-se com:

|   |   |
| --- | --- |
| ![Marcar][1] | Rever dispositivos suportados |
| ![Marcar][1] | Reveja as coisas que deve saber |
| ![Marcar][1] | Análise controlada validação da adiazur híbrida Azure |
| ![Marcar][1] | Selecione o seu cenário com base na sua infraestrutura de identidade |
| ![Marcar][1] | Reveja no local apoio da AD UPN para a adesão híbrida azure |

## <a name="review-supported-devices"></a>Rever dispositivos suportados

A adesão da Hybrid Azure ad suporta uma ampla gama de dispositivos Windows. Uma vez que a configuração para dispositivos que executam versões mais antigas do Windows requer passos adicionais ou diferentes, os dispositivos suportados são agrupados em duas categorias:

### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Windows 10
- Windows Server 2016
  - **Nota:** Clientes da nuvem Nacional Azure exigem versão 1809
- Windows Server 2019

Para dispositivos que executam o sistema operativo Windows desktop, a versão suportada está listada neste artigo Informações de lançamento do [Windows 10](/windows/release-information/). Como uma boa prática, a Microsoft recomenda-lhe o upgrade para a versão mais recente do Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior windows

- Windows 8.1
- O suporte do Windows 7 terminou a 14 de janeiro de 2020. Para mais informações, o [suporte para o Windows 7 terminou](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Para obter informações sobre o Windows Server 2008 e 2008 R2, consulte [Prepare-se para o windows server 2008 final de suporte](https://www.microsoft.com/cloud-platform/windows-server-2008).

Como primeiro passo de planeamento, deve rever o seu ambiente e determinar se precisa de suportar dispositivos de nível inferior windows.

## <a name="review-things-you-should-know"></a>Reveja as coisas que deve saber

### <a name="unsupported-scenarios"></a>Cenários não suportados
- A adesão à Hybrid Azure AD não é atualmente suportada se o seu ambiente consistir numa única floresta ad synchronizing dados de identidade para mais de um inquilino Azure AD.

- A adesão a Hybrid Azure AD não é suportada para o Windows Server que executa a função de Controlador de Domínio (DC).

- A adesão a Hybrid Azure AD não é suportada em dispositivos de nível inferior do Windows quando utilizar roaming credencial ou roaming de perfil de utilizador ou perfil obrigatório.

- O Server Core OS não suporta qualquer tipo de registo do dispositivo.

### <a name="os-imaging-considerations"></a>Considerações de imagem do OS
- Se estiver a contar com a Ferramenta de Preparação do Sistema (Sysprep) e se estiver a utilizar uma imagem **pré-Windows 10 1809** para instalação, certifique-se de que a imagem não é de um dispositivo que já está registado com a AD Azure como AD Hybrid Azure.

- Se estiver a contar com um instantâneo da Máquina Virtual (VM) para criar VMs adicionais, certifique-se de que o instantâneo não é de um VM que já está registado com a AD Azure Como AD Hybrid Azure.

- Se estiver a utilizar filtro de [escrita unificado](/windows-hardware/customize/enterprise/unified-write-filter) e tecnologias semelhantes que alteram claramente o disco no reboot, devem ser aplicadas depois de o dispositivo ser AD Hybrid Azure. Permitir tais tecnologias antes da conclusão da adesão do Hybrid Azure AD resultará em que o dispositivo se desloque a cada reboot

### <a name="handling-devices-with-azure-ad-registered-state"></a>Dispositivos de manuseamento com estado registado pela Azure AD
Se os dispositivos de domínio do Windows 10 forem [Azure AD registados no](overview.md#getting-devices-in-azure-ad) seu inquilino, poderá levar a um duplo estado de AD Hybrid Azure e dispositivo registado em Azure AD. Recomendamos a atualização para o Windows 10 1803 (com kB4489894 aplicado) ou acima para abordar automaticamente este cenário. Nos lançamentos anteriores a 1803, terá de remover manualmente o estado registado pela Azure AD antes de permitir a adesão da Hybrid Azure AD. Em 1803 e acima de lançamentos, foram feitas as seguintes alterações para evitar este duplo estado:

- Qualquer estado registado em Azure AD existente para um utilizador seria automaticamente removido <i>após a adesão do Hybrid Azure AD e os mesmos registos</i>de utilizador em . Por exemplo, se o Utilizador A tiver um estado registado em AD Azure no dispositivo, o estado duplo para o Utilizador A só é limpo quando o utilizador A iniciar sessão no dispositivo. se houver vários utilizadores no mesmo dispositivo, o estado duplo é limpo individualmente quando esses utilizadores fazem login.
- Pode evitar que o seu dispositivo de domínio se junte ao registo da AD Azure adicionando o seguinte valor de registo ao HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001.
- No Windows 10 1803, se tiver o Windows Hello for Business configurado, o utilizador precisa de reinstalar o Windows Hello for Business após a limpeza do estado duplo. Esta questão foi abordada com kB4512509

> [!NOTE]
> O dispositivo registado em Azure AD não será automaticamente removido se for gerido pela Intune.

### <a name="additional-considerations"></a>Considerações adicionais
- Se o seu ambiente utilizar infraestruturas de ambiente de trabalho virtuais (VDI), consulte [a identidade do dispositivo e a virtualização do ambiente](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure)de trabalho.

- A adesão à Hybrid Azure AD é suportada para TPM 2.0 compatível com FIPS e não suportada para TPM 1.2. Se os seus dispositivos tiverem TPM 1.2 compatível com FIPS, deve desativá-los antes de prosseguir com a adesão da Hybrid Azure AD. A Microsoft não fornece nenhuma ferramenta para desativar o modo FIPS para TPMs, uma vez que está dependente do fabricante tpm. Contacte o seu hardware OEM para obter suporte. 

- A partir do lançamento do Windows 10 1903, os TPMs 1.2 não são utilizados com a adesão híbrida do Azure AD e os dispositivos com esses TPMs serão considerados como se não tivessem um TPM.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Análise controlada validação da adiazur híbrida Azure

Quando todos os pré-requisitos estiverem em vigor, os dispositivos Windows registar-se-ão automaticamente como dispositivos no seu inquilino Azure AD. O estado destas identidades de dispositivo em Azure AD é referido como a adesão híbrida azure AD. Mais informações sobre os conceitos abrangidos por este artigo podem ser encontradas no artigo Introdução à gestão de identidade de [dispositivos no Diretório Ativo do Azure.](overview.md)

As organizações podem querer fazer uma validação controlada do AD Híbrido Azure antes de permitir em toda a sua organização tudo de uma vez. Reveja o artigo [validação controlada de AD azure híbrido para](hybrid-azuread-join-control.md) entender como realizá-lo.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selecione o seu cenário com base na sua infraestrutura de identidade

A Hybrid Azure AD junta-se a trabalhos com ambos, ambientes geridos e federados, dependendo se a UPN é repreensível ou não. Consulte a parte inferior da página para tabela em cenários suportados.  

### <a name="managed-environment"></a>Ambiente gerido

Um ambiente gerido pode ser implantado através de [Password Hash Sync (PHS)](/azure/active-directory/hybrid/whatis-phs) ou [Pass Through Authentication (PTA)](/azure/active-directory/hybrid/how-to-connect-pta) com [Um Único Sinal Sem Emenda .](/azure/active-directory/hybrid/how-to-connect-sso)

Estes cenários não requerem que configure um servidor da federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um fornecedor de identidade que suporte os seguintes requisitos. Se tiver um ambiente federado utilizando serviços da Federação de Diretórios Ativos (AD FS), então os requisitos abaixo já são suportados.

- **WiAorMULTIAUTHN reivindica:** Esta alegação é necessária para fazer a adesão híbrida azure para dispositivos de nível inferior Windows.
- **Protocolo WS-Trust:** Este protocolo é necessário para autenticar os dispositivos ad sinuosos do Windows atual ida e voltado com a AD Azure. Quando estiver a utilizar AD FS, tem de ativar os seguintes pontos finais wS-Trust:`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Tanto as **adfs/services/trust/2005/windowstransport** ou **adfs/services/trust/13/windowstransport** devem ser ativadas apenas como pontos finais intranet e NÃO devem ser expostas como pontos finais virados para a extranet através do Proxy de Aplicação Web. Para saber mais sobre como desativar os pontos finais do WS-Trust Windows, consulte [desativar os pontos finais do WS-Trust Windows no proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pode ver quais os pontos finais ativados através da consola de gestão AD FS em**pontos finais**de **serviço** > .

> [!NOTE]
> A Azure AD não suporta smartcards ou certificados em domínios geridos.

A partir da versão 1.1.819.0, o Azure AD Connect fornece um assistente para configurar a associação do Azure AD híbrido. O assistente permite-lhe simplificar significativamente o processo de configuração. Se instalar a versão necessária do Azure AD Connect não é uma opção para si, consulte [como configurar manualmente](hybrid-azuread-join-manual.md)o registo do dispositivo . 

Com base no cenário que corresponde à sua infraestrutura de identidade, consulte:

- [Configure hybrid Azure Ative Directory junta-se para ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configure hybrid Azure Ative Directory junta-se para ambiente gerido](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Reveja no local os utilizadores de AD UPN suportam a adesão à Hybrid Azure AD

Por vezes, os utilizadores de AD no local UPNs podem ser diferentes dos seus UPNs AD Azure. Nestes casos, a adesão ao Windows 10 Hybrid Azure AD fornece suporte limitado para UPNs AD no local com base no método de [autenticação](/azure/security/fundamentals/choose-ad-authn), tipo de domínio e versão Windows 10. Existem dois tipos de UPNs AD no local que podem existir no seu ambiente:

- Utilizadores enrugáveis UPN: Uma UPN resaída tem um domínio verificado válido, que está registado com um registo de domínio. Por exemplo, se contoso.com é o domínio primário em Azure AD, contoso.org é o domínio primário em anúncios no local propriedade de Contoso e [verificado em Azure AD](/azure/active-directory/fundamentals/add-custom-domain)
- Utilizadores não-enrsaídaS UPN: Uma UPN não-enrcorrida não tem um domínio verificado. Só é aplicável dentro da rede privada da sua organização. Por exemplo, se contoso.com é o domínio primário em Azure AD, contoso.local é o domínio primário no local, mas não é um domínio verificável na internet e apenas utilizado dentro da rede de Contoso.

> [!NOTE]
> As informações nesta secção aplicam-se apenas a uma UPN dos utilizadores no local. Não é aplicável a um sufixo de domínio de computador no local (exemplo: computer1.contoso.local).

A tabela abaixo fornece detalhes sobre o suporte para estas UPNs AD no local no Windows 10 Hybrid Azure AD juntam-se

| Tipo de ad upn no local | Tipo de domínio | Versão do Windows 10 | Descrição |
| ----- | ----- | ----- | ----- |
| Routable | Federados | A partir de 1703 lançamento | Disponível em Geral |
| Não-encaminhamento | Federados | A partir do lançamento de 1803 | Disponível em Geral |
| Routable | Geridos | A partir do lançamento de 1803 | Geralmente disponível, Azure AD SSPR no ecrã de bloqueio do Windows não é suportado |
| Não-encaminhamento | Geridos | Não suportado | |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configure hybrid Azure Ative Directory juntam-se para ambiente](hybrid-azuread-join-federated-domains.md)
> federado[Configure híbrido Azure Ative Diretório juntam-se para ambiente gerido](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
