---
title: Começar com inscrição combinada - Diretório Ativo Azure
description: Ativar a autenticação combinada de Multi-Factore AD AD e o registo de redefinição de senha de autosserviço (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f454b0296a3463d7346c2945b21162e5a38c0eb7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652510"
---
# <a name="enable-combined-security-information-registration-preview"></a>Ativar o registo combinado de informações de segurança (pré-visualização)

Antes de permitir a nova experiência, reveja o artigo Registo combinado de informações de [segurança (pré-visualização)](concept-registration-mfa-sspr-combined.md) para garantir que compreende a funcionalidade e os efeitos desta funcionalidade.

![Experiência melhorada do registo combinado de informações de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| O registo combinado de informações de segurança para a autenticação de multi-factores Azure e o reset de senha de autosserviço azure Ative (Azure AD) é uma funcionalidade de pré-visualização pública da Azure AD. Para mais informações sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .|
|     |

> [!NOTE]
> As organizações que permitiram a pré-visualização anterior para registar e gerir informações de segurança devem completar os passos abaixo para permitir a experiência de pré-visualização melhorada. Para as organizações que não fizerem a troca, a 8 de outubro de 2019, a Microsoft irá trocar os utilizadores da pré-visualização anterior para registar e gerir informações de segurança para a experiência melhorada. 
> 
> Se não tiver ativado nenhuma versão da pré-visualização, a sua organização não será impactada.

## <a name="enable-combined-registration"></a>Permitir o registo combinado

Complete estas etapas para permitir o registo combinado:

1. Inscreva-se no portal Azure como administrador de utilizador ou administrador global.
2. Vá para as > **definições** > de utilizador do **Diretório Ativo do Azure****Gerencie as definições de pré-visualização**do utilizador .
3. No âmbito do Utilizador pode utilizar funcionalidades de **pré-visualização para registar e gerir informações**de segurança, opte por ativar para um grupo de utilizadores **Selecionados** ou para **Todos os** utilizadores.

   ![Ativar a experiência combinada de pré-visualização de informações de segurança para todos os utilizadores](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefónica não estarão disponíveis para utilizadores de Multi-Factor autenticação e SSPR em inquilinos ad's free/trial Azure. As mensagens SMS não são afetadas por esta alteração. As opções de chamada telefónica ainda estarão disponíveis para os utilizadores em inquilinos da Azure AD pagos.

> [!NOTE]
> Depois de ativar o registo combinado, os utilizadores que se registem ou confirmem o seu número de telefone ou aplicação móvel através da nova experiência podem utilizá-los para autenticação multi-factor e SSPR, se esses métodos estiverem ativados nas políticas de Autenticação multi-Factor e SSPR. Se, em seguida, desativar esta experiência, os `https://aka.ms/ssprsetup` utilizadores que acederem à página de registo sSPR anterior serão obrigados a efetuar a autenticação de vários fatores antes de poderem aceder à página.

Se configurar a Lista de Atribuição de Zonas no Internet Explorer, os seguintes sites têm de estar na mesma zona:

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>Políticas de acesso condicional para registo combinado

A segurança quando e como os utilizadores se registam para a Autenticação multi-factor Do Azure e o reset de palavra-passe self-service é agora possível com as ações do utilizador na política de Acesso Condicional. Esta funcionalidade de pré-visualização está disponível para organizações que permitiram a [pré-visualização](../authentication/concept-registration-mfa-sspr-combined.md)combinada do registo. Esta funcionalidade pode ser ativada em organizações onde pretendem que os utilizadores se registem para a Autenticação Multi-Factor E SSPR azure a partir de um local central, como uma localização de rede confiável durante o embarque em RH. Para obter mais informações sobre a criação de localizações fidedignas no Acesso Condicional, consulte o artigo Qual é a condição de localização no Acesso Condicional do [Diretório Ativo azure?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Criar uma política para exigir o registo a partir de um local de confiança

A seguinte política aplica-se a todos os utilizadores selecionados, que tentam registar-se utilizando a experiência de registo combinado, e bloqueiam o acesso a menos que estejam a ligar-se a partir de um local marcado como rede fidedigna.

![Criar uma política da AC para controlar o registo de informações de segurança](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. No **portal Azure,** navegue até ao**Acesso Condicional** de**Segurança** >  **do Diretório** > Ativo azure
1. Selecione **Nova política**
1. Em Nome, insira um nome para esta política. Por exemplo, Registo combinado de **Informações de Segurança em Redes Fidedignas**
1. Em **Atribuições,** clique em **Utilizadores e grupos,** e selecione os utilizadores e grupos que pretende que esta política se aplique

   > [!WARNING]
   > Os utilizadores devem estar ativados para a [pré-visualização combinada](../authentication/howto-registration-mfa-sspr-combined.md)do registo .

1. Em **aplicações ou ações cloud**, selecione as ações do **Utilizador,** verifique **registar informações de segurança (pré-visualização)**
1. Em **condições** > **locais**
   1. Configurar **Sim**
   1. Incluir **Qualquer local**
   1. Excluir **todos os locais confiáveis**
   1. Clique na lâmina **de** localização
   1. Clique **na** lâmina Condições
1. Sob **controlos** > de acesso**Grant**
   1. Clique no **acesso ao bloco**
   1. Em seguida, clique em **Selecionar**
1. Definir **ativar a política** para **on**
1. Em seguida, clique em **Criar**

## <a name="next-steps"></a>Passos seguintes

[Forçar os utilizadores a reregistar métodos de autenticação](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Métodos disponíveis para autenticação multi-factor e SSPR](concept-authentication-methods.md)

[Configurar o reset da palavra-passe de autosserviço](howto-sspr-deployment.md)

[Configurar o Multi-Factor Authentication do Azure](howto-mfa-getstarted.md)

[Registo combinado de informações de segurança combinadas de resolução de problemas](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Qual é a condição de localização no Acesso Condicional do Diretório Ativo Azure?](../conditional-access/location-condition.md)
