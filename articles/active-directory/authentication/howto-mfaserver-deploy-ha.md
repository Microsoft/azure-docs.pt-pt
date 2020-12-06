---
title: Alta disponibilidade para Azure MFA Server - Azure Ative Directory
description: Implemente várias instâncias do Azure Multi-Factor Authentication Server em configurações que proporcionam alta disponibilidade.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23cf7abeb0aaf8bc3939296a307edb07ce5fcf0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742481"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configure Servidor de Autenticação Multi-Factor Azure para alta disponibilidade

Para obter uma elevada disponibilidade com a sua implementação MFA do Azure Server, é necessário implementar vários servidores MFA. Esta secção fornece informações sobre um design equilibrado de carga para atingir os seus alvos de alta disponibilidade na sua implementação do Azure MFS Server.

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores (MFA) durante os eventos de entrada devem utilizar a autenticação multi-factor Azure AD baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="mfa-server-overview"></a>Visão geral do servidor do MFA

A arquitetura de serviço do Azure MFA Server compreende vários componentes, como mostrado no seguinte diagrama:

 ![Componentes de arquitetura do servidor MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Um Servidor MFA é um Servidor Windows que tem o software de autenticação multi-factor Azure instalado. A instância do MFA Server deve ser ativada pelo Serviço MFA em Azure para funcionar. Mais de um Servidor MFA pode ser instalado no local.

O primeiro MFA Server que está instalado é o principal Servidor MFA após a ativação pelo Serviço MFA Azure por padrão. O servidor principal do MFA tem uma cópia escrita da base de dados PhoneFactor.pfdata. As instalações subsequentes de instâncias do MFA Server são conhecidas como subordinados. Os subordinados do MFA têm uma cópia replicada apenas de leitura da base de dados PhoneFactor.pfdata. Os servidores MFA replicam informações através da Chamada de Procedimento Remoto (RPC). Todos os MFA Severs devem ser unidos coletivamente ou autónomos para replicar informação.

Tanto os MFA master como os servidores subordinados do MFA comunicam com o Serviço MFA quando é necessária autenticação de dois fatores. Por exemplo, quando um utilizador tenta aceder a uma aplicação que requer autenticação de dois fatores, o utilizador será primeiro autenticado por um fornecedor de identidade, como o Ative Directory (AD).

Após a autenticação bem sucedida com a AD, o Servidor MFA comunicará com o Serviço MFA. O MFA Server aguarda a notificação do Serviço MFA para permitir ou negar o acesso do utilizador à aplicação.

Se o servidor principal do MFA ficar offline, as autenticações ainda podem ser processadas, mas as operações que requerem alterações na base de dados do MFA não podem ser processadas. (Exemplos incluem: a adição de utilizadores, alterações PIN de autosserviço, alteração da informação do utilizador ou acesso ao portal do utilizador)

## <a name="deployment"></a>Implementação

Considere os seguintes pontos importantes para o equilíbrio de carga Azure MFA Server e seus componentes relacionados.

* **Utilizando o padrão RADIUS para obter uma elevada disponibilidade**. Se estiver a utilizar servidores MFA Azure como servidores RADIUS, pode potencialmente configurar um Servidor MFA como alvo de autenticação RADIUS primário e outros Servidores MFA Azure como alvos de autenticação secundária. No entanto, este método para obter uma elevada disponibilidade pode não ser prático, pois deve esperar que ocorra um período de tempo de tempo quando a autenticação falha no alvo de autenticação primária antes de poder ser autenticado contra o alvo de autenticação secundária. É mais eficiente carregar o equilíbrio do tráfego RADIUS entre o cliente RADIUS e os Servidores RADIUS (neste caso, os Servidores MFA Azure atuando como servidores RADIUS) para que possa configurar os clientes RADIUS com um único URL que possam apontar.
* **Necessidade de promover manualmente subordinados de MFA.** Se o servidor MFA mestre Azure ficar offline, os servidores Azure MFA secundários continuam a processar os pedidos de MFA. No entanto, até que um servidor MFA principal esteja disponível, os administradores não podem adicionar utilizadores ou modificar as definições de MFA, e os utilizadores não podem fazer alterações usando o portal do utilizador. Promover um MFA subordinado ao papel principal é sempre um processo manual.
* **Separabilidade dos componentes.** O Azure MFA Server compreende vários componentes que podem ser instalados na mesma instância do Windows Server ou em diferentes instâncias. Estes componentes incluem o Portal do Utilizador, o Serviço Web de Aplicações Móveis e o adaptador ADFS (agente). Esta separabilidade permite utilizar o Proxy da Aplicação Web para publicar o Portal do Utilizador e o Servidor Web de Aplicações Móveis a partir da rede de perímetro. Esta configuração adiciona à segurança geral do seu design, como mostrado no diagrama seguinte. O Portal do Utilizador MFA e o Servidor Web de Aplicações Móveis também podem ser implantados em configurações equilibradas de carga HA.

   ![Servidor MFA com rede de perímetro](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **A palavra-passe única (OTP) sobre SMS (também conhecida como SMS de ida) requer a utilização de sessões pegajosas se o tráfego for equilibrado em carga**. O SMS unidireccionário é uma opção de autenticação que faz com que o Servidor MFA envie aos utilizadores uma mensagem de texto contendo uma OTP. O utilizador entra na OTP numa janela rápida para completar o desafio MFA. Se carregar o equilíbrio Azure MFA Servers, o mesmo servidor que serviu o pedido inicial de autenticação deve ser o servidor que recebe a mensagem OTP do utilizador; se outro MFA Server receber a resposta OTP, o desafio de autenticação falha. Para obter mais informações, consulte [uma palavra-passe de uma vez sobre SMS Adicionada ao Servidor MFA Azure](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **As implementações equilibradas de carga do Portal do Utilizador e do Serviço Web de Aplicações Móveis requerem sessões pegajosas**. Se estiver a equilibrar o Portal do Utilizador MFA e o Serviço Web de Aplicações Móveis, cada sessão tem de permanecer no mesmo servidor.

## <a name="high-availability-deployment"></a>Implantação de alta disponibilidade

O diagrama seguinte mostra uma implementação completa equilibrada de ha de Azure MFA e seus componentes, juntamente com a ADFS para referência.

 ![Implementação do Azure MFA Server HA](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Note os seguintes itens para a área numerada correspondente do diagrama anterior.

1. Os dois Servidores Azure MFA (MFA1 e MFA2) são equilibrados em carga (mfaapp.contoso.com) e estão configurados para utilizar uma porta estática (4443) para replicar a base de dados PhoneFactor.pfdata. O Web Service SDK está instalado em cada um dos Servidor MFA para permitir a comunicação sobre a porta TCP 443 com os servidores ADFS. Os servidores MFA são implantados numa configuração apátrida equilibrada em carga. No entanto, se quiser utilizar o OTP em vez de SMS, deve utilizar o equilíbrio de cargas imponente.
   ![Azure MFA Server - Servidor de aplicações HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Como o RPC utiliza portas dinâmicas, não é aconselhável abrir firewalls até à gama de portas dinâmicas que o RPC pode potencialmente utilizar. Se tiver uma firewall **entre** os seus servidores de aplicações MFA, deve configurar o Servidor MFA para comunicar numa porta estática para o tráfego de replicação entre servidores subordinados e principais e abrir essa porta na sua firewall. Pode forçar a porta estática criando um valor de registo DWORD ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` a chamada ```Pfsvc_ncan_ip_tcp_port``` e definindo o valor para uma porta estática disponível. As ligações são sempre iniciadas pelos servidores MFA subordinados ao mestre, a porta estática só é necessária no mestre, mas uma vez que pode promover um subordinado para ser o mestre a qualquer momento, deve definir a porta estática em todos os Servidores MFA.

2. Os dois servidores de aplicações móveis Portal/MFA (MFA-UP-MAS1 e MFA-UP-MAS2) são de carga equilibrada numa configuração **imponente** (mfa.contoso.com). Lembre-se que as sessões pegajosas são um requisito para o equilíbrio de cargas no Portal do Utilizador MFA e no Serviço de Aplicações Móveis.
   ![Servidor Azure MFA - Portal do Utilizador e Serviço de Aplicações Móveis HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. A quinta do Servidor ADFS é equilibrada e publicada na Internet através de proxies ADFS equilibrados em carga na rede de perímetro. Cada Servidor ADFS utiliza o agente ADFS para comunicar com os Servidores MFA Azure utilizando um único URL equilibrado em carga (mfaapp.contoso.com) sobre a porta TCP 443.

## <a name="next-steps"></a>Passos seguintes

* [Instalar e configurar o Servidor MFA do Azure](howto-mfaserver-deploy.md)
