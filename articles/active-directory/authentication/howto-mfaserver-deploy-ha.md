---
title: Alta disponibilidade para o servidor MFA do Azure-Azure Active Directory
description: Implante várias instâncias do Azure Servidor de Autenticação Multifator em configurações que fornecem alta disponibilidade.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18f56665041fed301faf3b4b5f99c78c1d468f8e
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404310"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configurar Servidor de Autenticação Multifator do Azure para alta disponibilidade

Para obter alta disponibilidade com a implantação do Azure Server MFA, você precisa implantar vários servidores MFA. Esta seção fornece informações sobre um design de balanceamento de carga para atingir seus destinos de alta disponibilidade na implantação do servidor do Azure MFS.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Novos clientes que queiram exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes existentes que ativaram o servidor MFA antes de 1º de julho poderão baixar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="mfa-server-overview"></a>Visão geral do servidor MFA

A arquitetura de serviço do servidor do Azure MFA é composta por vários componentes, conforme mostrado no diagrama a seguir:

 ![Componentes da arquitetura do servidor MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Um servidor MFA é um Windows Server que tem o software de autenticação multifator do Azure instalado. A instância do servidor MFA deve ser ativada pelo serviço MFA no Azure para funcionar. Mais de um servidor MFA pode ser instalado localmente.

O primeiro servidor MFA instalado é o servidor do MFA mestre na ativação pelo serviço do Azure MFA por padrão. O servidor MFA mestre tem uma cópia gravável do banco de dados PhoneFactor. pfdata. As instalações subsequentes de instâncias do servidor MFA são conhecidas como subordinadas. As subordinadas do MFA têm uma cópia replicada somente leitura do banco de dados PhoneFactor. pfdata. Os servidores MFA replicam informações usando RPC (chamada de procedimento remoto). Todos os servidores MFA devem estar coletivamente ingressados no domínio ou autônomos para replicar informações.

Os servidores mestre MFA e o MFA subordinado se comunicam com o serviço MFA quando a autenticação de dois fatores é necessária. Por exemplo, quando um usuário tenta obter acesso a um aplicativo que requer autenticação de dois fatores, o usuário primeiro será autenticado por um provedor de identidade, como Active Directory (AD).

Após a autenticação bem-sucedida com o AD, o servidor MFA se comunicará com o serviço de MFA. O servidor MFA aguarda a notificação do serviço MFA para permitir ou negar o acesso do usuário ao aplicativo.

Se o servidor mestre do MFA ficar offline, as autenticações ainda poderão ser processadas, mas as operações que exigirem alterações no banco de dados MFA não poderão ser processadas. (Os exemplos incluem: a adição de usuários, alterações de PIN de autoatendimento, alteração de informações do usuário ou acesso ao portal do usuário)

## <a name="deployment"></a>Implementação

Considere os seguintes pontos importantes para o balanceamento de carga do servidor MFA do Azure e seus componentes relacionados.

* **Usando o padrão RADIUS para obter alta disponibilidade**. Se você estiver usando servidores do Azure MFA como servidores RADIUS, poderá configurar um servidor MFA como um destino de autenticação RADIUS primário e outros servidores do Azure MFA como destinos de autenticação secundários. No entanto, esse método para obter alta disponibilidade pode não ser prático, pois você deve aguardar um período de tempo limite para ocorrer quando a autenticação falhar no destino de autenticação primário antes que você possa ser autenticado em relação ao destino de autenticação secundário. É mais eficiente balancear a carga do tráfego RADIUS entre o cliente RADIUS e os servidores RADIUS (nesse caso, os servidores do Azure MFA atuando como servidores RADIUS) para que você possa configurar os clientes RADIUS com uma única URL para a qual eles possam apontar.
* É **necessário promover os subordinados do MFA manualmente**. Se o servidor do Azure MFA mestre ficar offline, os servidores secundários do Azure MFA continuarão processando as solicitações de MFA. No entanto, até que um servidor de MFA mestre esteja disponível, os administradores não podem adicionar usuários nem modificar as configurações de MFA, e os usuários não podem fazer alterações usando o portal do usuário. Promover um MFA subordinado à função mestre é sempre um processo manual.
* **Separabilidade de componentes**. O servidor MFA do Azure consiste em vários componentes que podem ser instalados na mesma instância do Windows Server ou em instâncias diferentes. Esses componentes incluem o portal do usuário, o serviço Web de aplicativos móveis e o adaptador ADFS (agente). Esse separabilidade torna possível usar o proxy de aplicativo Web para publicar o portal do usuário e o servidor Web do aplicativo móvel por meio da rede de perímetro. Essa configuração adiciona a segurança geral do seu design, conforme mostrado no diagrama a seguir. O portal do usuário do MFA e o servidor Web do aplicativo móvel também podem ser implantados em configurações com balanceamento de carga de HA.

   ![Servidor MFA com uma rede de perímetro](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **A OTP (senha de uso único) sobre o SMS (também conhecido como SMS unidirecional) requer o uso de sessões adesivas se o tráfego tiver balanceamento de carga**. O SMS unidirecional é uma opção de autenticação que faz com que o servidor MFA envie aos usuários uma mensagem de texto contendo uma OTP. O usuário insere a OTP em uma janela de prompt para concluir o desafio de MFA. Se você balancear a carga de servidores MFA do Azure, o mesmo servidor que serviu a solicitação de autenticação inicial deverá ser o servidor que recebe a mensagem de OTP do usuário; Se outro servidor MFA receber a resposta de OTP, o desafio de autenticação falhará. Para obter mais informações, consulte [senha de uma vez sobre o SMS adicionado ao servidor do Azure MFA](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **As implantações com balanceamento de carga do portal do usuário e do serviço Web de aplicativos móveis exigem sessões adesivas**. Se você estiver fazendo o balanceamento de carga do portal do usuário do MFA e do serviço Web do aplicativo móvel, cada sessão precisará permanecer no mesmo servidor.

## <a name="high-availability-deployment"></a>Implantação de alta disponibilidade

O diagrama a seguir mostra uma implementação completa com balanceamento de carga de HA do Azure MFA e seus componentes, juntamente com o ADFS para referência.

 ![Implementação de HA do servidor MFA do Azure](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Observe os itens a seguir para a área numerada correspondente do diagrama anterior.

1. Os dois servidores MFA do Azure (MFA1 e MFA2) têm balanceamento de carga (mfaapp.contoso.com) e são configurados para usar uma porta estática (4443) para replicar o banco de dados PhoneFactor. pfdata. O SDK do serviço Web é instalado em cada servidor MFA para habilitar a comunicação pela porta TCP 443 com os servidores ADFS. Os servidores MFA são implantados em uma configuração com balanceamento de carga sem monitoração de estado. No entanto, se você quisesse usar o OTP sobre o SMS, deverá usar o balanceamento de carga com estado.
   ![servidor MFA do Azure-](./media/howto-mfaserver-deploy-ha/mfaapp.png) de HA do servidor de aplicativos

   > [!NOTE]
   > Como o RPC usa portas dinâmicas, não é recomendável abrir firewalls até o intervalo de portas dinâmicas que a RPC pode usar potencialmente. Se você tiver um firewall **entre** seus servidores de aplicativos MFA, deverá configurar o servidor MFA para se comunicar em uma porta estática para o tráfego de replicação entre servidores subordinados e mestres e abrir essa porta no firewall. Você pode forçar a porta estática criando um valor de Registro DWORD em ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` chamado ```Pfsvc_ncan_ip_tcp_port``` e definindo o valor para uma porta estática disponível. As conexões são sempre iniciadas pelos servidores do MFA subordinados ao Mestre, a porta estática só é necessária no mestre, mas como você pode promover um subordinado para ser o mestre a qualquer momento, você deve definir a porta estática em todos os servidores MFA.

2. Os dois servidores de aplicativos móveis do portal do usuário/MFA (MFA-UP-MAS1 e MFA-UP-MAS2) têm balanceamento de carga em uma configuração com **estado** (MFA.contoso.com). Lembre-se de que as sessões adesivas são um requisito para o balanceamento de carga do portal do usuário do MFA e do serviço de aplicativo móvel.
   ![servidor MFA do Azure-portal do usuário e HA do serviço de aplicativo móvel](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. O farm do servidor ADFS tem balanceamento de carga e publicação na Internet por meio de proxies ADFS com balanceamento de carga na rede de perímetro. Cada servidor ADFS usa o agente do ADFS para se comunicar com os servidores do Azure MFA usando uma única URL de balanceamento de carga (mfaapp.contoso.com) pela porta TCP 443.

## <a name="next-steps"></a>Passos seguintes

* [Instalar e configurar o servidor MFA do Azure](howto-mfaserver-deploy.md)
