---
title: Plan hybrid Azure Ative Directy aderir - Azure Ative Directory
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
ms.openlocfilehash: cadba181ea7d6a12ca64c78f3c7c58654d5f756f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500813"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Como: Planeie o seu Azure Ative Directory híbrido junte-se à implementação

Da mesma forma que um utilizador, um dispositivo é outra identidade central que pretende proteger e usá-lo para proteger os seus recursos a qualquer momento e a partir de qualquer local. Pode atingir este objetivo trazendo e gerindo identidades de dispositivos em Azure AD usando um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Ao trazer os seus dispositivos para o Azure AD, maximiza a produtividade dos seus utilizadores através do início de sessão único (SSO) nos seus recursos na cloud e no local. Ao mesmo tempo, pode garantir o acesso à sua nuvem e recursos no local com [Acesso Condicional.](../conditional-access/overview.md)

Se tiver um ambiente de Ative Directory (AD) no local e quiser juntar os seus computadores ligados ao domínio AD ao Azure AD, pode fazê-lo fazendo a ad AD híbrida. Este artigo fornece-lhe os passos relacionados para implementar um AD híbrido Azure aderir ao seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com a [Introdução à gestão de identidade de dispositivos no Azure Ative Directory](./overview.md).

> [!NOTE]
> A versão mínima necessária para o controlador de domínio para a junção híbrida AZure AD do Windows 10 é o Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planeie a sua implementação

Para planear a sua implementação híbrida Azure AD, deve familiarizar-se com:

> [!div class="checklist"]
> - Rever dispositivos suportados
> - Reveja as coisas que deve saber
> - Revisão validação controlada da ad híbrida Azure aderir
> - Selecione o seu cenário com base na sua infraestrutura de identidade
> - Rever no local suporte da AD UPN para a ad híbrida Azure AD

## <a name="review-supported-devices"></a>Rever dispositivos suportados

A Hybrid AZure AD junta-se a suportes a uma vasta gama de dispositivos Windows. Uma vez que a configuração para dispositivos que executam versões mais antigas do Windows requer passos adicionais ou diferentes, os dispositivos suportados são agrupados em duas categorias:

### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Windows 10
- Windows Server 2016
  - **Nota:** Azure National cloud customers require versão 1803
- Windows Server 2019

Para os dispositivos que executam o sistema operativo windows desktop, a versão suportada está listada neste artigo [informações de lançamento do Windows 10](/windows/release-information/). Como uma boa prática, a Microsoft recomenda o upgrade para a versão mais recente do Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior do Windows

- Windows 8.1
- O suporte do Windows 7 terminou a 14 de janeiro de 2020. Para obter mais informações, consulte [o Suporte para o Windows 7 terminou.](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Para obter informações de suporte no Windows Server 2008 e 2008 R2, consulte [Prepare-se para o fim do suporte do Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

Como primeiro passo de planeamento, deve rever o seu ambiente e determinar se precisa de suportar dispositivos de nível inferior do Windows.

## <a name="review-things-you-should-know"></a>Reveja as coisas que deve saber

### <a name="unsupported-scenarios"></a>Cenários não suportados
- A ad azure híbrida não é atualmente suportada se o seu ambiente consiste de uma única floresta de AD sincronizando dados de identidade para mais de um inquilino AZure AD.

- A aderimento Híbrido AD não é suportado para o Windows Server executando a função de Controlador de Domínio (DC).

- A aderente híbrida AD não é suportada em dispositivos de nível baixo do Windows quando se utiliza roaming credencial ou roaming de perfil do utilizador ou perfil obrigatório.

- O Server Core OS não suporta qualquer tipo de registo do dispositivo.

- A Ferramenta de Migração do Estado utilizador (USMT) não funciona com o registo do dispositivo.  

### <a name="os-imaging-considerations"></a>Considerações de imagem de SO
- Se estiver a contar com a Ferramenta de Preparação do Sistema (Sysprep) e se estiver a utilizar uma imagem **pré-Windows 10 1809** para instalação, certifique-se de que a imagem não é de um dispositivo que já está registado com Azure AD como AD Híbrido Azure.

- Se estiver a contar com uma foto de Máquina Virtual (VM) para criar VMs adicionais, certifique-se de que o instantâneo não é de um VM que já está registado no AZure AD como ad AD Híbrido Azure.

- Se estiver a utilizar [o Filtro de Escrita Unificado](/windows-hardware/customize/enterprise/unified-write-filter) e tecnologias semelhantes que claras alterações no disco no reboot, devem ser aplicadas depois de o dispositivo ser híbrido Azure AD. Permitir tais tecnologias antes da conclusão da junção Híbrida Azure AD resultará em que o dispositivo não seja ligado em cada reboot

### <a name="handling-devices-with-azure-ad-registered-state"></a>Dispositivos de manuseamento com estado registado Azure AD
Se os dispositivos associados ao domínio do Windows 10 forem [Azure AD registados no nome](overview.md#getting-devices-in-azure-ad) do seu inquilino, poderá levar a um duplo estado de ad AD Híbrido Azure e dispositivo registado Azure AD. Recomendamos o upgrade para o Windows 10 1803 (com KB4489894 aplicado) ou acima para abordar automaticamente este cenário. Nas versões pré-1803, terá de remover manualmente o estado registado Azure AD antes de permitir a junção do AD Híbrido Azure. Em 1803 e acima de 1803, foram efetuadas as seguintes alterações para evitar este duplo estado:

- Qualquer estado registado em Azure AD existente para um utilizador seria automaticamente removido <i>após o dispositivo ser híbrido Azure AD e os mesmos registos de utilizador dentro</i> Por exemplo, se o Utilizador A tiver um estado AD AD Azure registado no dispositivo, o duplo estado do Utilizador A só é limpo quando o Utilizador A entra no dispositivo. Se houver vários utilizadores no mesmo dispositivo, o estado duplo é limpo individualmente quando estes utilizadores fazem login. Além de remover o estado registado Azure AD, o Windows 10 também irá desinsupear o dispositivo do Intune ou de outros MDM, se a inscrição ocorrer como parte do registo AD Azure através de inscrição automática.
- O estado registado da Azure AD em quaisquer contas locais no dispositivo não é afetado por esta alteração. Só é aplicável às contas de domínio. Assim, o estado registado Azure AD nas contas locais não é removido automaticamente mesmo após a súmã do utilizador, uma vez que o utilizador não é um utilizador de domínio. 
- Pode evitar que o seu dispositivo de alistamento de domínio seja registado a Azure AD adicionando o seguinte valor de registo à HKLM\SOFTWARE\Policies\Microsoft\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001.
- No Windows 10 1803, se tiver o Windows Hello para o Negócios configurado, o utilizador precisa de reen configurar o Windows Hello para o Negócios após a limpeza do duplo estado. Esta questão foi abordada com o KB4512509

> [!NOTE]
> Apesar de o Windows 10 remover automaticamente o estado registado AZURE AD localmente, o objeto do dispositivo em Azure AD não é imediatamente eliminado se for gerido pelo Intune. Pode validar a remoção do estado registado Azure AD executando dsregcmd/status e considerar que o dispositivo não é Azure AD registado com base nisso.

### <a name="additional-considerations"></a>Considerações adicionais
- Se o seu ambiente utilizar infraestruturas de ambientes de trabalho virtuais (VDI), consulte [a identidade do dispositivo e a virtualização do ambiente de trabalho](./howto-device-identity-virtual-desktop-infrastructure.md).

- A aderente híbrida AZure AD é suportada para TPM 2.0 compatível com FIPS e não suportada para TPM 1.2. Se os seus dispositivos tiverem TPM 1.2 compatível com FIPS, deve desativá-los antes de prosseguir com a ad Hybrid Azure. A Microsoft não fornece quaisquer ferramentas para desativar o modo FIPS para TPMs, uma vez que depende do fabricante TPM. Entre em contato com o seu hardware OEM para obter suporte. 

- A partir do lançamento do Windows 10 1903, os TPMs 1.2 não são utilizados com a junta híbrida Azure AD e os dispositivos com esses TPMs serão considerados como se não tivessem um TPM.

- As alterações da UPN são suportadas apenas a partir da atualização do Windows 10 2004. Para dispositivos anteriores à atualização do Windows 10 2004, os utilizadores teriam problemas de Acesso SSO e Condicional nos seus dispositivos. Para resolver este problema, é necessário desagregá-lo a partir do Azure AD (executar "dsregcmd/leave" com privilégios elevados) e voltar a juntar-se (acontece automaticamente). No entanto, os utilizadores que se inscrevam no Windows Hello for Business não enfrentam este problema.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Revisão validação controlada da ad híbrida Azure aderir

Quando todos os pré-requisitos estiverem em vigor, os dispositivos Windows registar-se-ão automaticamente como dispositivos no seu inquilino AZure AD. O estado destas identidades do dispositivo em Azure AD é referido como a ad Azure híbrido. Mais informações sobre os conceitos abrangidos por este artigo podem ser encontradas no artigo [Introdução à gestão de identidade de dispositivo no Azure Ative Directory](overview.md).

As organizações podem querer fazer uma validação controlada da ad híbrida Azure antes de permitir em toda a sua organização de uma só vez. Reveja a validação controlada do artigo [da Ad híbrida Azure](hybrid-azuread-join-control.md) para entender como fazê-lo.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selecione o seu cenário com base na sua infraestrutura de identidade

A Hybrid AZure AD junta-se a obras com ambientes geridos e federados, dependendo se a UPN é encaminhável ou não. Consulte a parte inferior da página para a tabela em cenários suportados.  

### <a name="managed-environment"></a>Ambiente gerido

Um ambiente gerido pode ser implementado através do [Password Hash Sync (PHS)](../hybrid/whatis-phs.md) ou [do Passe Através da Autenticação (PTA)](../hybrid/how-to-connect-pta.md) com [sinal único sem costura .](../hybrid/how-to-connect-sso.md)

Estes cenários não requerem que configures um servidor da federação para autenticação.

> [!NOTE]
> [A autenticação em nuvem utilizando o lançamento encenado](../hybrid/how-to-connect-staged-rollout.md) só é suportada a partir da atualização do Windows 10 1903

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um fornecedor de identidade que apoie os seguintes requisitos. Se tiver um ambiente federado utilizando os Serviços da Federação de Diretórios Ativos (FS AD), então os requisitos abaixo já estão suportados.

- **WIAORMULTIAUTHN reivindicação:** Esta alegação é necessária para fazer a junção híbrida Azure AD para dispositivos de nível baixo do Windows.
- **Protocolo WS-Trust:** Este protocolo é necessário para autenticar dispositivos híbridos Azure AD atuais do Windows com Azure AD. Quando estiver a utilizar o AD FS, tem de ativar os seguintes pontos finais WS-Trust: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Tanto **adfs/serviços/trust/2005/windowstransport** ou **adfs/services/trust/13/windowstransport** devem ser ativados apenas como pontos finais virados para a intranet e NÃO devem ser expostos como pontos finais virados para a extranet através do Proxy da Aplicação Web. Para saber mais sobre como desativar WS-Trust pontos finais do Windows, consulte [pontos finais do Windows desativar WS-Trust no proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pode ver quais os pontos finais que estão ativados através da consola de gestão AD FS em **Service**  >  **Endpoints**.

> [!NOTE]
> A Azure AD não suporta smartcards ou certificados em domínios geridos.

A partir da versão 1.1.819.0, o Azure AD Connect fornece um assistente para configurar a associação do Azure AD híbrido. O assistente permite-lhe simplificar significativamente o processo de configuração. Se instalar a versão necessária do Azure AD Connect não for uma opção para si, consulte [como configurar manualmente](hybrid-azuread-join-manual.md)o registo do dispositivo . 

Com base no cenário que combina com a sua infraestrutura de identidade, consulte:

- [Configure híbrido Azure Ative Directy junta-se ao ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configure híbrido Azure Ative Directy junta-se para ambiente gerido](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Rever no local suporte upn utilizadores de AD para a ad Hybrid Azure

Por vezes, os seus utilizadores de AD no local, UPNs, podem ser diferentes dos seus UPNs AD Azure. Nestes casos, a adere ao AD Azure Híbrido do Windows 10 fornece suporte limitado para UPNs AD no local com base no método de [autenticação,](../hybrid/choose-ad-authn.md)tipo de domínio e versão Windows 10. Existem dois tipos de UPNs AD no local que podem existir no seu ambiente:

- Utilizadores encaminháveis UPN: Uma UPN encaminhável tem um domínio verificado válido, que está registado com um registo de domínio. Por exemplo, se contoso.com é o domínio principal em AZure AD, contoso.org é o domínio primário em AD no local pertencente à Contoso e [verificado em Azure AD](../fundamentals/add-custom-domain.md)
- Utilizadores não encaminháveis UPN: Uma UPN não-encaminhável não tem um domínio verificado. É aplicável apenas dentro da rede privada da sua organização. Por exemplo, se contoso.com é o domínio principal em Azure AD, contoso.local é o domínio principal em AD no local, mas não é um domínio verificável na internet e apenas usado dentro da rede de Contoso.

> [!NOTE]
> As informações desta secção aplicam-se apenas a um utilizadores no local UPN. Não é aplicável a um sufixo de domínio de computador no local (exemplo: computer1.contoso.local).

O quadro abaixo fornece detalhes sobre o suporte para estes UPNs AD no local no Windows 10 Hybrid AD

| Tipo de UPN AD no local | Tipo de domínio | Versão do Windows 10 | Description |
| ----- | ----- | ----- | ----- |
| Roteável | Federados | A partir de 1703 lançamento | Disponível em Geral |
| Não-encaminhável | Federados | A partir de 1803 lançamento | Disponível em Geral |
| Roteável | Seja gerido | A partir de 1803 lançamento | Geralmente disponível, O Azure AD SSPR no ecrã de bloqueio do Windows não é suportado. A UPN no local deve ser sincronizada com o     `onPremisesUserPrincipalName` atributo em Azure AD |
| Não-encaminhável | Seja gerido | Não suportado | |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configure híbrido Azure Ative Directy junta-se ao ambiente](hybrid-azuread-join-federated-domains.md) 
>  federado [Configure híbrido Azure Ative Directy junta-se para ambiente gerido](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
