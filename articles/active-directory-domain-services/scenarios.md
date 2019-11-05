---
title: Cenários comuns de implantação para Azure AD Domain Services | Microsoft Docs
description: Saiba mais sobre alguns dos cenários comuns e casos de uso para Azure Active Directory Domain Services fornecer valor e atender às necessidades dos negócios.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 489f4a527a5afaf1bab5e2065137a5011d45baa6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474450"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Casos de uso comuns e cenários para Azure Active Directory Domain Services

O Azure Active Directory Domain Services (AD DS do Azure) fornece serviços de domínio gerenciados, como ingresso no domínio, diretiva de grupo, LDAP (Lightweight Directory Access Protocol) e autenticação Kerberos/NTLM. O Azure AD DS integra-se ao seu locatário existente do Azure AD, o que possibilita que os usuários entrem usando suas credenciais existentes. Você usa esses serviços de domínio sem a necessidade de implantar, gerenciar e aplicar patch nos controladores de domínio na nuvem, o que fornece um aumento mais suave dos recursos locais para o Azure.

Este artigo descreve alguns cenários de negócios comuns em que o Azure AD DS fornece valor e atende a essas necessidades.

## <a name="secure-administration-of-azure-virtual-machines"></a>Administração segura de máquinas virtuais do Azure

Para permitir que você use um único conjunto de credenciais do AD, as VMs (máquinas virtuais) do Azure podem ser unidas a um domínio gerenciado do Azure AD DS. Essa abordagem reduz os problemas de gerenciamento de credenciais, como a manutenção de contas de administrador local em cada VM ou a contas e senhas separadas entre ambientes.

As VMs que ingressaram em um domínio gerenciado do Azure AD DS também podem ser gerenciadas e protegidas usando a política de grupo. As linhas de base de segurança necessárias podem ser aplicadas às VMs para bloqueá-las de acordo com as diretrizes de segurança corporativa. Por exemplo, você pode usar recursos de gerenciamento de política de grupo para restringir os tipos de aplicativos que podem ser iniciados na VM.

![Administração simplificada de máquinas virtuais do Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Vejamos um cenário de exemplo comum. Como servidores e outras infra-estruturas atingem o fim da vida útil, a contoso deseja mover os aplicativos atualmente hospedados localmente para a nuvem. Seu padrão de ti atual exige que os servidores que hospedam aplicativos corporativos devem ser ingressados no domínio e gerenciados usando a diretiva de grupo. O administrador de ti da Contoso prefere o ingresso no domínio de VMs implantadas no Azure para facilitar a administração, pois os usuários podem entrar usando suas credenciais corporativas. Quando ingressado no domínio, as VMs também podem ser configuradas para atender às linhas de base de segurança necessárias usando objetos de política de grupo (GPOs). A contoso prefere não implantar, monitorar e gerenciar seus próprios controladores de domínio no Azure.

O Azure AD DS é uma ótima opção para esse caso de uso. Um domínio gerenciado do Azure AD DS permite que você ingresse em VMs de domínio, use um único conjunto de credenciais e aplique a política de grupo. Como um domínio gerenciado, você não precisa configurar e manter os controladores de domínio por conta própria.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação se aplicam a este exemplo de caso de uso:

* O Azure AD DS domínios gerenciados usam uma única estrutura de UO (unidade organizacional) simples por padrão. Todas as VMs ingressadas no domínio estão em uma única UO. Se desejar, você pode criar UOs personalizadas.
* O Azure AD DS usa um GPO interno para os contêineres usuários e computadores. Para controle adicional, você pode criar GPOs personalizados e direcioná-los para UOs personalizadas.
* O Azure AD DS dá suporte ao esquema de objeto de computador do AD base. Você não pode estender o esquema do objeto do computador.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Aplicativos locais de comparação de precisão e deslocamento que usam autenticação de associação LDAP

Como um cenário de exemplo, a contoso tem um aplicativo local que foi adquirido de um ISV há muitos anos. O aplicativo está atualmente no modo de manutenção pelo ISV e solicitar alterações ao aplicativo é extremamente caro. Esse aplicativo tem um front-end baseado na Web que coleta as credenciais do usuário usando um formulário da Web e autentica os usuários executando uma associação LDAP ao ambiente de AD DS local.

![Associação LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

A contoso gostaria de migrar esse aplicativo para o Azure. O aplicativo deve continuar funcionando no estado em que se encontra, sem necessidade de alterações. Além disso, os usuários devem ser capazes de autenticar usando suas credenciais corporativas existentes e sem treinamento adicional. Ele deve ser transparente para os usuários finais onde o aplicativo está em execução.

Para esse cenário, o Azure AD DS permite que os aplicativos executem associações LDAP como parte do processo de autenticação. Os aplicativos locais herdados podem se deslocar e mudar para o Azure e continuar a autenticar os usuários sem qualquer alteração na configuração ou na experiência do usuário.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação se aplicam a este exemplo de caso de uso:

* Certifique-se de que o aplicativo não precise modificar/gravar no diretório. Não há suporte para o acesso de gravação LDAP para um domínio gerenciado do Azure AD DS.
* Você não pode alterar as senhas diretamente em um domínio gerenciado do Azure AD DS. Os usuários finais podem alterar sua senha usando o mecanismo de alteração de senha de autoatendimento do Azure AD ou no diretório local. Essas alterações são sincronizadas automaticamente e disponíveis no domínio gerenciado AD DS do Azure.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Aplicativos locais de comparação de precisão e deslocamento que usam LDAP leitura para acessar o diretório

Como o cenário de exemplo anterior, vamos supor que a contoso tenha um aplicativo de linha de negócios (LOB) local que foi desenvolvido há quase uma década. Este aplicativo tem reconhecimento de diretório e foi projetado para usar LDAP para ler informações/atributos sobre os usuários de AD DS. O aplicativo não modifica atributos ou, de outra forma, grava no diretório.

A contoso deseja migrar esse aplicativo para o Azure e desativar o hardware local de envelhecimento que hospeda esse aplicativo no momento. O aplicativo não pode ser reescrito para usar APIs de diretório modernas, como o API do Graph do Azure AD baseado em REST. Uma opção de comparação de precisão e deslocamento é desejada onde o aplicativo pode ser migrado para ser executado na nuvem, sem modificar o código ou reescrever o aplicativo.

Para ajudar nesse cenário, o Azure AD DS permite que os aplicativos executem leituras LDAP no domínio gerenciado para obter as informações de atributo de que precisa. O aplicativo não precisa ser reescrito, portanto, um deslocamento e uma mudança no Azure permitem que os usuários continuem a usar o aplicativo sem perceber que há uma alteração no local em que é executado.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação se aplicam a este exemplo de caso de uso:

* Certifique-se de que o aplicativo não precise modificar/gravar no diretório. Não há suporte para o acesso de gravação LDAP para um domínio gerenciado do Azure AD DS.
* Verifique se o aplicativo não precisa de um esquema de Active Directory personalizado/estendido. Não há suporte para extensões de esquema no AD DS do Azure.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrar um aplicativo de serviço ou daemon local para o Azure

Alguns aplicativos incluem várias camadas, em que uma das camadas precisa executar chamadas autenticadas para uma camada de back-end, como um banco de dados. As contas de serviço do AD são comumente usadas nesses cenários. Ao migrar e deslocar aplicativos para o Azure, o Azure AD DS permite que você continue a usar contas de serviço da mesma maneira. Você pode optar por usar a mesma conta de serviço que é sincronizada do seu diretório local para o Azure AD ou criar uma UO personalizada e, em seguida, criar uma conta de serviço separada nessa UO. Com qualquer abordagem, os aplicativos continuam a funcionar da mesma maneira de fazer chamadas autenticadas para outras camadas e serviços.

![Conta de serviço usando WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Neste cenário de exemplo, a contoso tem um aplicativo de cofre de software personalizado que inclui um front-end da Web, um SQL Server e um servidor FTP de back-end. A autenticação integrada do Windows usando contas de serviço autentica o front-end da Web para o servidor FTP. O front-end da Web é configurado para ser executado como uma conta de serviço. O servidor de back-end é configurado para autorizar o acesso da conta de serviço para o front-end da Web. A contoso não deseja implantar e gerenciar suas próprias VMs do controlador de domínio na nuvem para mover esse aplicativo para o Azure.

Para esse cenário, os servidores que hospedam o front-end da Web, o SQL Server e o servidor FTP podem ser migrados para VMs do Azure e ingressados em um domínio gerenciado do Azure AD DS. As VMs podem usar a mesma conta de serviço em seu diretório local para fins de autenticação do aplicativo, que é sincronizado por meio do Azure AD usando o Azure AD Connect.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação se aplicam a este exemplo de caso de uso:

* Verifique se os aplicativos usam um nome de usuário + senha para autenticação. Não há suporte para autenticação com base em certificado ou cartão inteligente no Azure AD DS.
* Você não pode alterar as senhas diretamente em um domínio gerenciado do Azure AD DS. Os usuários finais podem alterar sua senha usando o mecanismo de alteração de senha de autoatendimento do Azure AD ou no diretório local. Essas alterações são sincronizadas automaticamente e disponíveis no domínio gerenciado AD DS do Azure.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Implantações de serviços de área de trabalho remota do Windows Server no Azure

Você pode usar o Azure AD DS para fornecer serviços de domínio gerenciado para servidores de área de trabalho remota implantados no Azure. Para obter mais informações sobre esse cenário de implantação, consulte [como integrar o Azure AD Domain Services com sua implantação do RDS][windows-rds].

## <a name="domain-joined-hdinsight-clusters-preview"></a>Clusters HDInsight ingressados no domínio (visualização)

Você pode configurar um cluster do Azure HDInsight que tenha ingressado em um domínio gerenciado AD DS do Azure com o Apache Ranger habilitado. Você pode criar e aplicar políticas de Hive por meio do Apache Ranger e permitir que usuários, como cientistas de dados, se conectem ao hive usando ferramentas baseadas em ODBC como Excel ou tableau. Continuamos a trabalhar para adicionar outras cargas de trabalho, como HBase, Spark e Storm ao HDInsight ingressado no domínio.

Para obter mais informações sobre esse cenário de implantação, consulte [como configurar clusters HDInsight ingressados no domínio][hdinsight]

## <a name="next-steps"></a>Passos seguintes

Para começar, [criar e configurar uma instância de Azure Active Directory Domain Services][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
