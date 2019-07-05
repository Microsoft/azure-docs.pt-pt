---
title: Como planear a implementação de associação híbrida do Azure Active Directory no Azure Active Directory (Azure AD) | Documentos da Microsoft
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
ms.openlocfilehash: c476c2b326045db37c54a358d68f4b5f8bbaed9a
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509605"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Como: Planear a sua implementação híbrida do Azure Active Directory

De forma semelhante a um utilizador, um dispositivo é outra identidade de núcleos que pretende proteger e utilizá-la para proteger os seus recursos em qualquer altura e a partir de qualquer local. Pode realizar esse objetivo por género e a gestão de identidades de dispositivos no Azure AD através de um dos seguintes métodos:

- Associação ao Azure AD
- Associação ao Azure AD Híbrido
- Registo do Azure AD

Ao trazer os seus dispositivos para o Azure AD, maximiza a produtividade dos seus utilizadores através do início de sessão único (SSO) nos seus recursos na cloud e no local. Ao mesmo tempo, pode proteger o acesso aos seus recursos na cloud e no local com o [acesso condicional](../active-directory-conditional-access-azure-portal.md).

Se tiver um ambiente do Active Directory (AD) no local e que pretende associar os computadores associados a um domínio do AD para o Azure AD, é possível fazer isso ao fazer a associação do Azure AD híbrido. Este artigo fornece a com os passos relacionados para implementar um Azure AD híbrido Junte-se em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com o [introdução à gestão de identidade de dispositivo no Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> O mínimo necessário funcional do domínio e os níveis funcionais de floresta para Windows 10 a associação do Azure AD híbrido é o Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planeie a sua implementação

Para planear a implementação do Azure AD híbrido, deve familiarizar-se com:

|   |   |
| --- | --- |
| ![Marcar][1] | Rever suportado dispositivos |
| ![Marcar][1] | Deve saber de coisas de revisão |
| ![Marcar][1] | Reveja a validação controlada de associação do Azure AD híbrido |
| ![Marcar][1] | Selecione o seu cenário com base na sua infraestrutura de identidade |
| ![Marcar][1] | Revisão locais UPN do AD de suporte para híbrida associação do Azure AD |

## <a name="review-supported-devices"></a>Rever suportado dispositivos

Associação do híbrida do Azure AD suporta uma ampla gama de Windows de dispositivos. Como a configuração para dispositivos que executam versões mais antigas do Windows requer passos adicionais ou diferentes, os dispositivos suportados são agrupados em duas categorias:

### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Windows 10
- Windows Server 2016
- Windows Server 2019

Para dispositivos que executam o sistema de operativo ambiente de trabalho do Windows, versão suportada está listado neste artigo [informações de versão do Windows 10](https://docs.microsoft.com/windows/release-information/). Como melhor prática, a Microsoft recomenda a que atualização para a versão mais recente do Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior do Windows

- Windows 8.1
- Windows 7. Para obter informações sobre o Windows 7, consulte este artigo [descontinuar o suporte para o Windows 7](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Como uma primeira etapa de planeamento, deve rever o seu ambiente e determinar se é necessário suportar dispositivos de nível inferior do Windows.

## <a name="review-things-you-should-know"></a>Deve saber de coisas de revisão

Associação do Azure AD híbrido atualmente não é suportada se o seu ambiente consiste numa única floresta do AD sincronização de dados de identidade para mais do que um inquilino do Azure AD.

Associação do Azure AD híbrido atualmente não é suportada quando utiliza a infraestrutura de ambiente de trabalho virtual (VDI).

Associação do Azure AD híbrido não é suportada para TPMs compatíveis com FIPS. Se os seus dispositivos têm TPMs compatíveis com FIPS, deve desativá-las antes de continuar com a associação do Azure AD híbrido. A Microsoft não fornece quaisquer ferramentas para desabilitar o modo de FIPS para TPMs, pois é dependente de fabricante TPM. Entre em contato com seu hardware OEM para obter suporte.

Associação do Azure AD híbrido não é suportada para o Windows Server a executar a função de controlador de domínio (DC).

Associação ao Azure AD híbrido não é suportada em dispositivos de nível inferior do Windows ao utilizar a mobilidade de credenciais ou perfil de utilizador itinerantes.

Se está contando com a ferramenta de preparação do sistema (Sysprep) e se estiver a utilizar um **anteriores ao Windows 10 1809** para a instalação de imagem, certifique-se de que imagem não é de um dispositivo que já está registado no Azure AD como a associação ao Azure AD híbrido.

Se está contando com um instantâneo da Máquina Virtual (VM) para criar VMs adicionais, certifique-se de que o instantâneo não está a partir de uma VM que já está registrada no Azure AD como a associação ao Azure AD híbrido.

Se o seu domínio do Windows 10 associados a um dispositivos já estão [do Azure AD registado](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) ao seu inquilino, é altamente recomendável remover esse Estado antes de ativar a associação ao Azure AD híbrido. Da versão do Windows 10 1809, as seguintes alterações foram feitas para evitar este estado duplo:

- Qualquer estado existente do Azure AD registado seria sejam removido automaticamente depois do dispositivo está associado ao Azure AD híbrido.
- Pode impedir que o dispositivo associado ao domínio estejam do Azure AD registado ao adicionar esta chave de registo - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = dword:00000001.
- Esta alteração já está disponível para a versão do Windows 10 1803 com KB4489894 aplicada. No entanto, se tiver o Windows Hello para empresas configurada, o utilizador é necessário a re-configuração do Windows Hello para empresas após o estado de duplo de limpeza do wsu.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Reveja a validação controlada de associação do Azure AD híbrido

Quando todos os pré-requisitos estão em vigor, serão registado automaticamente dispositivos Windows como dispositivos no seu inquilino do Azure AD. O estado destas identidades de dispositivo no Azure AD é referido como a associação do Azure AD híbrido. Mais informações sobre os conceitos abordados neste artigo podem ser encontradas nos artigos [introdução à gestão de identidade de dispositivo no Azure Active Directory](overview.md) e [planear a sua associação do Azure Active Directory híbrida implementação](hybrid-azuread-join-plan.md).

As organizações, poderão querer fazer uma validação controlada de associação do híbrida do Azure AD antes de a ativar toda a organização inteira ao mesmo tempo. Consulte o artigo [controlado a validação de associação do Azure AD híbrido](hybrid-azuread-join-control.md) para compreender como fazer isso.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selecione o seu cenário com base na sua infraestrutura de identidade

Associação ao Azure AD híbrido funciona com ambientes e não geridos federados.  

### <a name="managed-environment"></a>Ambiente gerenciado

Um ambiente gerenciado pode ser implementado por meio [sincronização de Hash de palavra-passe (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) ou [passar através de autenticação (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) com [sessão único totalmente integrado](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Estes cenários não requerem que configurar um servidor de Federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Ambiente federado, deve ter um fornecedor de identidade que suporta os seguintes requisitos:

- **Protocolo WS-Trust:** Esse protocolo é necessário a autenticação Windows atual híbrida do Azure AD associado a um dispositivos com o Azure AD.
- **WIAORMULTIAUTHN de solicitação:** Esta afirmação é necessária fazer a associação do Azure AD híbrido para dispositivos de nível inferior do Windows.

Se tiver um ambiente federado, usando os serviços de Federação do Active Directory (AD FS), os requisitos acima já são suportados.

> [!NOTE]
> O Azure AD não suporta smart cards ou certificados em domínios geridos.

A partir da versão 1.1.819.0, o Azure AD Connect fornece um assistente para configurar a associação do Azure AD híbrido. O assistente permite-lhe simplificar significativamente o processo de configuração. Se instalar a versão necessária do Azure AD Connect não é uma opção para si, veja [como configurar manualmente o registo de dispositivos](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

Com base no cenário que corresponde à sua infraestrutura de identidade, consulte:

- [Configurar a associação ao Azure Active Directory de híbrido para o ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configurar a associação ao Azure Active Directory de híbrido para o ambiente gerido](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Revisão de suporte no local UPN do AD para associação ao Azure AD híbrido

Às vezes, o ambiente AD UPNs poderiam ser diferentes da sua UPNs do AD do Azure. Nesses casos, associação ao Windows 10 híbrido do Azure AD fornece suporte limitado para locais UPNs AD com base na [método de autenticação](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), tipo de domínio e versão do Windows 10. Existem dois tipos de locais UPNs do AD que podem existir no seu ambiente:

- UPN encaminhável: Um UPN encaminhável tem um domínio verificado válido, o que está registado com uma entidade de registo do domínio. Por exemplo, se contoso.com é o domínio principal no Azure AD, contoso.org é o domínio principal no local AD pertencentes a Contoso e [verificados no Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- UPN não encaminháveis internos: Um UPN não encaminháveis internos não tem um domínio verificado. É aplicável apenas dentro da rede privada da sua organização. Por exemplo, se contoso.com é o domínio principal no Azure AD, contoso. local é o domínio principal no AD no local, mas não é um domínio verificável na internet e somente usados dentro da Contoso da rede.

A tabela seguinte fornece detalhes sobre o suporte para esses locais UPNs AD na associação ao Windows 10 híbrido do Azure AD

| Tipo de locais UPN do AD | Tipo de domínio | Versão do Windows 10 | Descrição |
| ----- | ----- | ----- | ----- |
| Encaminhável | Federado | Da versão 1703 | Disponível em geral |
| Não encaminháveis | Federado | Versão 1803 | Disponível em geral |
| Encaminhável | Managed | Não suportado | |
| Não encaminháveis | Managed | Não suportado | |

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Associação ao Azure Active Directory configurar híbrido para o ambiente federado](hybrid-azuread-join-federated-domains.md)
> [associação ao Azure Active Directory configurar híbrido para o ambiente gerido](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
