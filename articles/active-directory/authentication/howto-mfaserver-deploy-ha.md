---
title: Configurar o servidor MFA do Azure para elevada disponibilidade - Azure Active Directory
description: Implemente várias instâncias do Azure multi-factor Authentication Server nas configurações que fornecem elevada disponibilidade.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba8a1976fa9fc2a02f7f3e8419de10ebba7df950
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316308"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configurar o servidor do Azure multi-factor Authentication para elevada disponibilidade

Para obter alta disponibilidade com a sua implementação do servidor do MFA do Azure, terá de implementar vários servidores MFA. Esta seção fornece informações sobre um design com balanceamento de carga para alcançar seus destinos de elevada disponibilidade, a implementação de servidor de MFS do Azure.

## <a name="mfa-server-overview"></a>Descrição geral do servidor MFA

A arquitetura de serviço do servidor MFA do Azure é composto por vários componentes conforme mostrado no diagrama seguinte:

 ![Arquitetura de servidor MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Um servidor de MFA é um servidor do Windows que tem o software de multi-factor Authentication instalado. A instância do servidor MFA tem de ser ativada pelo serviço de MFA no Azure para a função. Mais de um servidor de MFA pode ser instalado no local.

O primeiro servidor MFA instalado é o principal servidor MFA na ativação pelo serviço de MFA do Azure, por predefinição. O servidor MFA mestre tem uma cópia gravável da base de dados Pfdata. As instalações subsequentes de instâncias do servidor MFA são conhecidas como subordinados. Os subordinados MFA tem uma cópia só de leitura replicada da base de dados Pfdata. Servidores MFA de replicar informações usando a chamada de procedimento remoto (RPC). Todos os servidores de MFA tem coletivamente de estar associados a um domínio ou autónomo para replicar informações.

Mestre de MFA e servidores subordinados do MFA comuniquem com o serviço de MFA quando é necessária a autenticação de dois fatores. Por exemplo, quando um utilizador tenta obter acesso a uma aplicação que exija a autenticação de dois fatores, o utilizador será primeiro autenticado pelo fornecedor de identidade, como o Active Directory (AD).

Após a autenticação com êxito com o AD, o servidor MFA irá comunicar com o serviço de MFA. O servidor MFA espera pela notificação do serviço de MFA para permitir ou negar o acesso de utilizador à aplicação.

Se o servidor mestre do MFA ficar offline, as autenticações ainda podem ser processadas, mas não não possível processar as operações que exigem alterações no banco de dados de MFA. (Os exemplos incluem: a adição de utilizadores self-service as alterações PIN e alterar as informações de utilizador)

## <a name="deployment"></a>Implementação

Considere os seguintes pontos importantes para o servidor MFA do Azure e os respetivos componentes relacionados de balanceamento de carga.

* **Usando o padrão RADIUS para alcançar a elevada disponibilidade**. Se estiver a utilizar servidores MFA do Azure como servidores RADIUS, pode configurar um servidor de MFA potencialmente como um destino de autenticação RADIUS primário e de outros servidores de MFA do Azure como destinos de autenticação secundária. No entanto, este método para assegurar elevada disponibilidade pode não ser prático porque tem de aguardar um período de tempo limite ocorrer quando a autenticação falha no destino de autenticação primária antes de pode ser autenticado contra o destino de autenticação secundária. É mais eficiente para equilibrar o tráfego RADIUS entre o cliente RADIUS e os servidores RADIUS (no caso, os servidores de MFA do Azure que funcionam como servidores RADIUS), de modo a que pode configurar clientes RADIUS com uma única URL que eles podem apontar a.
* **Precisa de promover manualmente o MFA subordinados**. Se o servidor MFA do Azure principal fica offline, os servidores de MFA do Azure secundária continuar a processar pedidos MFA. No entanto, até que um servidor MFA mestre está disponível, os administradores não podem adicionar utilizadores ou modificar as definições da MFA e os utilizadores não podem efetuar alterações com o portal de utilizador. Promover um MFA subordinada para a função de mestre é sempre um processo manual.
* **Separability dos componentes**. O servidor de MFA do Azure é composto por vários componentes que podem ser instalados na mesma instância do Windows Server ou em diferentes instâncias. Estes componentes incluem o Portal de utilizador, o serviço Web da aplicação móvel e o adaptador ADFS (agente). Este separability torna possível utilizar o Proxy de aplicações Web para publicar o Portal de utilizador e o servidor de Web de aplicação móvel da rede de perímetro. Esta configuração adiciona a segurança geral de seu design, conforme mostrado no diagrama seguinte. O Portal de utilizador do MFA e o servidor de Web de aplicação de Mobile também podem ser implementadas em configurações de com balanceamento de carga HA.

   ![Servidor MFA com uma rede de perímetro](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Palavra-passe Monouso (OTP) ao longo do SMS (também conhecido como SMS unidirecional) requer a utilização de sessões adesivas, se for de tráfego com balanceamento de carga**. SMS unidirecional é uma opção de autenticação que faz com que o servidor MFA enviar uma mensagem de texto que contém uma OTP dos utilizadores. O usuário insere a OTP numa janela de linha de comandos para completar o desafio MFA. Se carregar saldo servidores MFA do Azure, o mesmo servidor que serviu o pedido de autenticação inicial tem de ser o servidor que recebe a mensagem OTP do usuário; Se a outro servidor de MFA receber a resposta OTP, o desafio de autenticação falha. Para obter mais informações, consulte [um tempo de palavra-passe através de SMS adicionados ao servidor MFA do Azure](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Implementações com balanceamento de carga do Portal de utilizador e serviço Web da aplicação móvel exigem sessões adesivas**. Se estiver balanceamento de carga do Portal de utilizador do MFA e o serviço Web da aplicação móvel, cada sessão tem de permanecer no mesmo servidor.

## <a name="high-availability-deployment"></a>Implementação de elevada disponibilidade

O diagrama seguinte mostra uma implementação de com balanceamento de carga HA completa do MFA do Azure e os respetivos componentes, juntamente com o ADFS para referência.

 ![MFA Server HA implementação do Azure](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Tenha em atenção os seguintes itens para a área do mesmo modo numerado do diagrama anterior.

1. Os servidores de MFA do Azure dois (MFA1 e MFA2) têm balanceada de carga (mfaapp.contoso.com) e estão configurados para utilizar uma porta estática (4443) para replicar a base de dados Pfdata. O Web Service SDK está instalado em cada servidor MFA para ativar a comunicação através da porta TCP 443 com os servidores do AD FS. Os servidores MFA são implementados numa configuração sem monitoração de estado com balanceamento de carga. No entanto, se quisesse utilizar OTP através de SMS, tem de utilizar o balanceamento de carga com monitoração de estado.
   ![Servidor MFA do Azure - HA do servidor de aplicações](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Uma vez RPC utiliza portas dinâmicas, não é recomendado para abrir a firewalls até o intervalo de portas dinâmicos, que pode usar o RPC. Se tiver uma firewall **entre** seus servidores de aplicações do MFA, deve configurar o servidor MFA para comunicar numa porta estática para o tráfego de replicação entre os servidores principais e subordinadas e abrir essa porta no firewall. Pode forçar a porta estática através da criação de um valor de registo DWORD em ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` chamado ```Pfsvc_ncan_ip_tcp_port``` e definindo o valor para uma porta estática disponível. Ligações sejam iniciadas sempre pelos servidores MFA subordinado a mestre, a porta estática é necessário apenas no mestre de, mas uma vez que pode promover subordinado seja o mestre em qualquer altura, deve definir a porta estática em todos os servidores de MFA.

2. Os dois servidores de aplicação de Mobile do MFA do Portal de utilizador (MFA-UP-MAS1 e MFA-UP-MAS2) são carga balanceada num **com monitoração de estado** configuration (mfa.contoso.com). Lembre-se de que a sessões adesivas são um requisito para o Portal de utilizador do MFA e o serviço de aplicações móveis de balanceamento de carga.
   ![Servidor MFA do Azure - Portal de utilizador e serviço de aplicações móveis HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. O farm de servidores do AD FS é balanceados em carga e publicado na Internet por meio de proxies ADFS com balanceamento de carga na rede de perímetro. Cada servidor do ADFS utiliza o agente do ADFS para comunicar com os servidores de MFA do Azure através de um URL com balanceamento de carga único (mfaapp.contoso.com) através da porta TCP 443.

## <a name="next-steps"></a>Passos Seguintes

* [Instalar e configurar o servidor MFA do Azure](howto-mfaserver-deploy.md)
