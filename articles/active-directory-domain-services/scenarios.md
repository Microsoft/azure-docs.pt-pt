---
title: Cenários comuns de implantação para serviços de domínio da AD Azure  Microsoft Docs
description: Conheça alguns dos cenários comuns e casos de utilização dos Serviços de Domínio de Diretório Ativo Azure para fornecer valor e satisfazer as necessidades do negócio.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917235"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Casos e cenários comuns para serviços de domínio de diretório ativo Azure

O Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a adesão ao domínio, a política de grupo, o protocolo de acesso ao diretório leve (LDAP) e a autenticação Kerberos /NTLM. O Azure AD DS integra-se com o seu inquilino Azure AD existente, o que permite aos utilizadores assinarem usando as suas credenciais existentes. Você usa estes serviços de domínio sem a necessidade de implementar, gerir e patch controladores de domínio na nuvem, o que fornece um elevador e mudança mais suaves de recursos no local para Azure.

Este artigo descreve alguns cenários de negócio comuns onde o Azure AD DS fornece valor e satisfaz essas necessidades.

## <a name="secure-administration-of-azure-virtual-machines"></a>Administração segura de máquinas virtuais Azure

Para permitir que utilize um único conjunto de credenciais aD, as máquinas virtuais Azure (VMs) podem ser unidas a um domínio gerido por Azure AD DS. Esta abordagem reduz questões de gestão credenciais, tais como a manutenção de contas de administrador local em cada VM ou contas separadas e senhas entre ambientes.

Os VMs que se juntam a um domínio gerido pelo Azure AD DS também podem ser geridos e protegidos usando a política de grupo. As linhas de base de segurança necessárias podem ser aplicadas aos VMs para os bloquear de acordo com as diretrizes de segurança corporativa. Por exemplo, pode utilizar capacidades de gestão de políticas de grupo para restringir os tipos de aplicações que podem ser lançadas no VM.

![Administração simplificada de máquinas virtuais Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Vamos olhar para um cenário de exemplo comum. À medida que os servidores e outras infraestruturas atingem o fim da vida, Contoso quer mover aplicações atualmente alojadas nas instalações para a nuvem. Os seus atuais mandatos padrão de TI que os servidores que hospedam aplicações corporativas devem ser associados e geridos usando a política do grupo. O administrador de TI da Contoso prefere juntar-se aos VMs implantados no Azure para facilitar a administração, uma vez que os utilizadores podem então assinar usando as suas credenciais corporativas. Quando unidos no domínio, os VMs também podem ser configurados para cumprir as linhas de segurança necessárias usando objetos de política de grupo (GPOs). Contoso prefere não implantar, monitorizar e gerir os seus próprios controladores de domínio em Azure.

O Azure AD DS é um ótimo ajuste para este caso de uso. Um domínio gerido por AD DS Azure permite-lhe aderir a VMs de domínio, usar um único conjunto de credenciais e aplicar a política de grupo. Como domínio gerido, não é preciso configurar e manter os controladores de domínio por si mesmo.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação aplicam-se a este caso de utilização de exemplo:

* Os domínios geridos pela Azure AD DS utilizam por defeito uma estrutura única e plana da Unidade Organizacional (OU). Todos os VMs unidos do domínio estão numa única U. Se desejar, pode criar OUs personalizados.
* O Azure AD DS utiliza um GPO incorporado cada um para os utilizadores e contentores de computadores. Para um controlo adicional, pode criar GPOs personalizados e direcioná-los para OUs personalizados.
* O Azure AD DS suporta o esquema de objeto de computador AD base. Não pode estender o esquema do objeto de computador.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Aplicações de elevação e mudança no local que utilizam a autenticação de ligação LDAP

Como cenário de amostragem, O Contoso tem uma aplicação no local que foi comprada a um ISV há muitos anos. A aplicação encontra-se atualmente em modo de manutenção pelo ISV e solicitar alterações à aplicação é proibitivamente cara. Esta aplicação tem um frontend baseado na Web que recolhe credenciais de utilizador usando um formulário web e depois autentica os utilizadores executando um lDAP ligado ao ambiente AD DS no local.

![Enlace LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso gostaria de migrar esta aplicação para Azure. A aplicação deve continuar a funcionar como está, sem necessidade de alterações. Além disso, os utilizadores devem poder autenticar usando as suas credenciais corporativas existentes e sem formação adicional. Deve ser transparente para os utilizadores finais onde a aplicação está em execução.

Para este cenário, o Azure AD DS permite que as aplicações executem ligações LDAP como parte do processo de autenticação. As aplicações legacy on-presing podem levantar e transformar-se em Azure e continuar a autenticar perfeitamente os utilizadores sem qualquer alteração na configuração ou experiência do utilizador.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação aplicam-se a este caso de utilização de exemplo:

* Certifique-se de que a aplicação não precisa de modificar/escrever para o diretório. O acesso ao LDAP não é suportado por um domínio gerido pelo Azure AD DS.
* Não é possível alterar as palavras-passe diretamente contra um domínio gerido pelo Azure AD DS. Os utilizadores finais podem alterar a sua palavra-passe utilizando o mecanismo de mudança de senha de autosserviço da Azure AD ou contra o diretório no local. Estas alterações são então automaticamente sincronizadas e disponíveis no domínio gerido pelo Azure AD DS.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Aplicações de elevação e mudança no local que usam LDAP ler para aceder ao diretório

Tal como o cenário de exemplo anterior, vamos supor que Contoso tem uma aplicação de linha de negócios (LOB) no local que foi desenvolvida há quase uma década. Esta aplicação é consciente de diretório e foi concebida para usar o LDAP para ler informações/atributos sobre utilizadores de DS AD. A aplicação não modifica atributos nem escreve de outra forma para o diretório.

Contoso quer migrar esta aplicação para o Azure e reformar o hardware envelhecido no local que acolhe atualmente esta aplicação. A aplicação não pode ser reescrita para usar APIs de diretório moderno, como a Microsoft Graph API baseada em REST. É desejada uma opção de elevação e mudança onde a aplicação pode ser migrada para ser executada na nuvem, sem modificar código ou reescrever a aplicação.

Para ajudar neste cenário, o Azure AD DS permite que as aplicações executem leituras LDAP contra o domínio gerido para obter a informação de atributo de que necessita. A aplicação não precisa de ser reescrita, pelo que um elevador e mudança para o Azure permite que os utilizadores continuem a usar a app sem se aperceberem que há uma mudança no local onde funciona.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação aplicam-se a este caso de utilização de exemplo:

* Certifique-se de que a aplicação não precisa de modificar/escrever para o diretório. O acesso ao LDAP não é suportado por um domínio gerido pelo Azure AD DS.
* Certifique-se de que a aplicação não necessita de um esquema de Diretório Ativo personalizado/alargado. As extensões de schema não são suportadas no Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrar um serviço no local ou uma aplicação de daemon para o Azure

Algumas aplicações incluem vários níveis, onde um dos níveis precisa de realizar chamadas autenticadas para um nível de backend, como uma base de dados. As contas de serviço aD são comumente usadas nestes cenários. Quando levanta e transfere aplicações para O Azure, o Azure AD DS permite-lhe continuar a utilizar as contas de serviço da mesma forma. Pode optar por utilizar a mesma conta de serviço sincronizada desde o seu diretório no local até à Azure AD ou criar uma Ou personalizada e, em seguida, criar uma conta de serviço separada nesse OU. Em qualquer uma das abordagens, as aplicações continuam a funcionar da mesma forma para fazer chamadas autenticadas para outros níveis e serviços.

![Conta de serviço usando wia](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Neste cenário de exemplo, A Contoso tem uma aplicação de cofre de software personalizada que inclui uma extremidade frontal web, um servidor SQL e um servidor FTP backend. A autenticação integrada pelo Windows utilizando contas de serviço autentica a extremidade frontal web do servidor FTP. A extremidade frontal da web é configurada para funcionar como uma conta de serviço. O servidor backend está configurado para autorizar o acesso da conta de serviço para a extremidade frontal web. Contoso não quer implementar e gerir os seus próprios VMs controladores de domínio na nuvem para mover esta aplicação para O Azure.

Para este cenário, os servidores que hospedam a extremidade frontal web, o servidor SQL e o servidor FTP podem ser migrados para VMs Azure e unidos a um domínio gerido por AD DS Azure. Os VMs podem então utilizar a mesma conta de serviço no seu diretório no local para fins de autenticação da aplicação, que é sincronizada através do Azure AD utilizando o Azure AD Connect.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação aplicam-se a este caso de utilização de exemplo:

* Certifique-se de que as aplicações utilizam um nome de utilizador e uma senha para autenticação. A autenticação baseada em certificado ou smartcard não é suportada pelo Azure AD DS.
* Não é possível alterar as palavras-passe diretamente contra um domínio gerido pelo Azure AD DS. Os utilizadores finais podem alterar a sua palavra-passe utilizando o mecanismo de mudança de senha de autosserviço da Azure AD ou contra o diretório no local. Estas alterações são então automaticamente sincronizadas e disponíveis no domínio gerido pelo Azure AD DS.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Implementações de serviços de desktop remoto seledores do Windows Server em Azure

Pode utilizar o Azure AD DS para fornecer serviços de domínio geridos a servidores de ambiente de trabalho remotos implantados no Azure. Para mais informações sobre este cenário de implementação, consulte como integrar os Serviços de [Domínio Azure AD com a sua implementação RDS][windows-rds].

## <a name="domain-joined-hdinsight-clusters"></a>Clusters HDInsight unidos pelo domínio

Pode configurar um cluster Azure HDInsight que se junta a um domínio gerido pelo Azure AD DS com apache Ranger ativado. Você pode criar e aplicar políticas de Colmeia através do Apache Ranger, e permitir que os utilizadores, como cientistas de dados, se conectem à Colmeia usando ferramentas baseadas em ODBC como Excel ou Tableau. Continuamos a trabalhar para adicionar outras cargas de trabalho, como HBase, Spark e Storm ao HDInsight de domínio.

Para mais informações sobre este cenário de implementação, consulte [como configurar clusters HDInsight unidos pelo domínio][hdinsight]

## <a name="next-steps"></a>Passos seguintes

Para começar, Crie e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
