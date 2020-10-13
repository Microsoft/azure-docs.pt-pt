---
title: Cenários comuns de implantação dos Serviços de Domínio AD Azure Microsoft Docs
description: Conheça alguns dos cenários e casos de utilização comuns para os Serviços de Domínio do Diretório Ativo Azure para fornecer valor e atender às necessidades do negócio.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: joflore
ms.openlocfilehash: 6c63609642b8dd8d1269222b06b57033d84ad8a0
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967907"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Casos e cenários de utilização comuns para serviços de domínio do diretório ativo Azure

A Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a adesão de domínio, a política de grupo, o protocolo de acesso ao diretório leve (LDAP) e a autenticação Kerberos /NTLM. O Azure AD DS integra-se com o seu inquilino AZure AD existente, o que permite aos utilizadores assinarem usando as suas credenciais existentes. Utiliza estes serviços de domínio sem a necessidade de implantar, gerir e remendar controladores de domínio na nuvem, o que proporciona um levantamento e mudança mais suaves dos recursos no local para Azure.

Este artigo descreve alguns cenários de negócio comuns onde a Azure AD DS fornece valor e satisfaz essas necessidades.

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Formas comuns de fornecer soluções identitárias na nuvem

Quando migrar cargas de trabalho existentes para a nuvem, as aplicações conscientes do diretório podem usar LDAP para ler ou escrever acesso a um diretório AD DS no local. As aplicações que funcionam no Windows Server são normalmente implementadas em máquinas virtuais (VMs) de domínio para que possam ser geridas de forma segura utilizando a Política de Grupo. Para autenticar os utilizadores finais, as aplicações podem também contar com a autenticação integrada do Windows, como a autenticação Kerberos ou a autenticação NTLM.

Os administradores de TI usam frequentemente uma das seguintes soluções para fornecer um serviço de identidade às aplicações que funcionam em Azure:

* Configure uma ligação VPN local-a-local entre cargas de trabalho que funcionam em Azure e um ambiente AD DS no local.
    * Os controladores de domínio no local fornecem então a autenticação através da ligação VPN.
* Crie controladores de domínio de réplica utilizando máquinas virtuais Azure (VMs) para estender o domínio/floresta AD DS a partir das instalações.
    * Os controladores de domínio que funcionam em VMs Azure fornecem autenticação e replicam informações de diretório entre o ambiente AD DS no local.
* Implementar um ambiente AD DS autónomo em Azure utilizando controladores de domínio que funcionam em VMs Azure.
    * Os controladores de domínio que funcionam em VMs Azure fornecem autenticação, mas não há nenhuma informação de diretório replicada a partir de um ambiente AD DS no local.

Com estas abordagens, as ligações VPN ao diretório no local tornam as aplicações vulneráveis a falhas ou falhas de rede transitórias. Se implementar controladores de domínio utilizando VMs em Azure, a equipa de TI deve gerir os VMs, então proteger, corrigir, monitorizar, fazer backup e resolver problemas.

O Azure AD DS oferece alternativas à necessidade de criar ligações VPN de volta a um ambiente AD DS no local ou executar e gerir VMs em Azure para fornecer serviços de identidade. Como um serviço gerido, a Azure AD DS reduz a complexidade para criar uma solução de identidade integrada para ambientes híbridos e apenas em nuvem.

> [!div class="nextstepaction"]
> [Compare O AD DS Azure com AZure AD e DS AD auto-gerido em VMs ou no local][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS para organizações híbridas

Muitas organizações gerem uma infraestrutura híbrida que inclui cargas de trabalho de aplicação em nuvem e no local. As aplicações antigas migradas para Azure como parte de uma estratégia de elevação e mudança podem usar ligações LDAP tradicionais para fornecer informações de identidade. Para apoiar esta infraestrutura híbrida, a informação de identidade de um ambiente AD DS no local pode ser sincronizada com um inquilino AD AZure. A Azure AD DS fornece então estas aplicações antigas em Azure com uma fonte de identidade, sem a necessidade de configurar e gerir a conectividade da aplicação de volta aos serviços de diretório no local.

Vejamos um exemplo para a Litware Corporation, uma organização híbrida que gere tanto os recursos no local como os recursos da Azure:

![Azure Ative Directory Domain Services para uma organização híbrida que inclui sincronização no local](./media/overview/synced-tenant.png)

* As aplicações e as cargas de trabalho do servidor que requerem serviços de domínio são implementadas numa rede virtual em Azure.
    * Isto pode incluir aplicações antigas migradas para Azure como parte de uma estratégia de elevação e mudança.
* Para sincronizar informações de identidade do seu diretório no local para o seu inquilino AZURE AD, a Litware Corporation implementa [o Azure AD Connect][azure-ad-connect].
    * A informação de identidade sincronizada inclui contas de utilizador e membros do grupo.
* A equipa de TI da Litware permite que o Azure AD DS para o seu inquilino AZure AD nesta, ou uma rede virtual esprevada.
* Aplicações e VMs implantados na rede virtual Azure podem então usar funcionalidades Azure AD DS como aderente de domínio, LDAP ler, ligação LDAP, autenticação NTLM e Kerberos, e Política de Grupo.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio gerido para sincronizar objetos de volta ao AZure AD.

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS para organizações só em nuvem

Um inquilino da AD Azure só na nuvem não tem uma fonte de identidade no local. As contas de utilizador e os membros do grupo, por exemplo, são criados e geridos diretamente no Azure AD.

Agora vamos olhar para um exemplo para Contoso, uma organização só em nuvem que usa Azure AD para identidade. Todas as identidades dos utilizadores, as suas credenciais e membros do grupo são criados e geridos em Azure AD. Não existe nenhuma configuração adicional do Azure AD Connect para sincronizar qualquer informação de identidade a partir de um diretório no local.

![Azure Ative Directory Domain Services para uma organização só em nuvem sem sincronização no local](./media/overview/cloud-only-tenant.png)

* As aplicações e as cargas de trabalho do servidor que requerem serviços de domínio são implementadas numa rede virtual em Azure.
* A equipa de TI da Contoso permite que o Azure AD DS para o seu inquilino AZure AD nesta, ou uma rede virtual esprevada.
* Aplicações e VMs implantados na rede virtual Azure podem então usar funcionalidades Azure AD DS como aderente de domínio, LDAP ler, ligação LDAP, autenticação NTLM e Kerberos, e Política de Grupo.

## <a name="secure-administration-of-azure-virtual-machines"></a>Administração segura de máquinas virtuais Azure

Para permitir que utilize um único conjunto de credenciais AD, as máquinas virtuais Azure (VMs) podem ser unidas a um domínio gerido Azure AD DS. Esta abordagem reduz as questões de gestão credenciais, tais como a manutenção de contas de administrador local em cada VM ou contas separadas e palavras-passe entre ambientes.

Os VM que se juntam a um domínio gerido também podem ser administrados e protegidos através da política de grupo. As linhas de base de segurança necessárias podem ser aplicadas aos VMs para os bloquear de acordo com as diretrizes de segurança da empresa. Por exemplo, pode utilizar as capacidades de gestão de políticas de grupo para restringir os tipos de aplicações que podem ser lançadas no VM.

![Administração simplificada de máquinas virtuais Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Vejamos um cenário comum. À medida que os servidores e outras infraestruturas atingem o fim de vida, a Contoso quer mover as aplicações atualmente alobradas nas instalações para a nuvem. Os seus atuais mandatos de TI de que os servidores que hospedam aplicações corporativas devem ser unidos e geridos através da política de grupo.

O administrador de TI da Contoso preferiria juntar-se aos VM implantados no Azure para facilitar a administração, uma vez que os utilizadores podem então assinar usando as suas credenciais corporativas. Quando ligados ao domínio, os VMs também podem ser configurados para cumprir as linhas de segurança necessárias utilizando objetos de política de grupo (GPOs). Contoso prefere não implantar, monitorizar e gerir os seus próprios controladores de domínio em Azure.

A azure AD DS é um ótimo ajuste para este caso de uso. Um domínio gerido permite-lhe juntar o domínio VMs, usar um único conjunto de credenciais e aplicar a política de grupo. E como é um domínio gerido, não é preciso configurar e manter os controladores de domínio.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação aplicam-se a este caso de utilização de exemplo:

* Os domínios geridos utilizam por defeito uma única estrutura da Unidade Organizacional (OU) plana. Todos os VMs unidos ao domínio estão num único U. Se desejar, pode criar [OUs personalizados][custom-ou].
* O Azure AD DS utiliza um GPO incorporado para os utilizadores e recipientes de computadores. Para um controlo adicional, pode [criar GPOs personalizados][create-gpo] e direcioná-los para OUs personalizados.
* O Azure AD DS suporta o esquema de objetos de computador AD base. Não pode estender o esquema do objeto do computador.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Aplicações de elevação e mudança no local que utilizam a autenticação de ligação LDAP

Como cenário de amostra, a Contoso tem uma aplicação no local que foi comprada a um ISV há muitos anos. A aplicação encontra-se atualmente em modo de manutenção pelo ISV e solicitar alterações à aplicação é proibitivamente caro. Esta aplicação tem um frontend baseado na web que recolhe credenciais de utilizador usando um formulário web e, em seguida, autentica os utilizadores através da realização de um LDAP ligado ao ambiente AD DS no local.

![Ligação LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso gostaria de migrar esta aplicação para Azure. A aplicação deve continuar a funcionar como está, sem necessidade de alterações. Além disso, os utilizadores devem poder autenticar usando as suas credenciais corporativas existentes e sem formação adicional. Deve ser transparente para os utilizadores finais onde a aplicação está em execução.

Para este cenário, a Azure AD DS permite que as aplicações executem ligações LDAP como parte do processo de autenticação. As aplicações antigas no local podem levantar e mudar para o Azure e continuar a autenticar os utilizadores sem qualquer alteração na configuração ou na experiência do utilizador.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação aplicam-se a este caso de utilização de exemplo:

* Certifique-se de que a aplicação não precisa de modificar/escrever para o diretório. LDAP escrever acesso a um domínio gerido não é suportado.
* Não é possível alterar palavras-passe diretamente contra um domínio gerido. Os utilizadores finais podem alterar a sua palavra-passe utilizando [o mecanismo de alteração de senha de autosserviço da Azure AD][sspr] ou contra o diretório no local. Estas alterações são então automaticamente sincronizadas e disponíveis no domínio gerido.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Aplicações de elevação e turno no local que utilizam lDAP ler para aceder ao diretório

À semelhança do cenário de exemplo anterior, vamos supor que a Contoso tem uma aplicação de linha de negócios (LOB) no local que foi desenvolvida há quase uma década. Esta aplicação tem conhecimento do diretório e foi concebida para utilizar o LDAP para ler informações/atributos sobre utilizadores a partir de DS AD. A aplicação não modifica atributos ou escreve de outra forma para o diretório.

A Contoso quer migrar esta aplicação para o Azure e retirar o hardware de envelhecimento no local que atualmente acolhe esta aplicação. A aplicação não pode ser reescrita para usar APIs de diretório moderno, como a API do Gráfico microsoft baseado em REST. É desejada uma opção de elevação e mudança onde a aplicação pode ser migrada para ser executada na nuvem, sem modificar código ou reescrever a aplicação.

Para ajudar neste cenário, o Azure AD DS permite que as aplicações executem leituras LDAP contra o domínio gerido para obter a informação de atributo de que necessita. A aplicação não precisa de ser reescrita, por isso um elevador e mudança para o Azure permite que os utilizadores continuem a usar a app sem se aperceberem que há uma mudança no seu funcionamento.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação aplicam-se a este caso de utilização de exemplo:

* Certifique-se de que a aplicação não precisa de modificar/escrever para o diretório. LDAP escrever acesso a um domínio gerido não é suportado.
* Certifique-se de que a aplicação não necessita de um esquema de Diretório Ativo personalizado/estendido. As extensões de schema não são suportadas em Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrar um serviço no local ou aplicação da daemon para a Azure

Algumas aplicações incluem vários níveis, onde um dos níveis precisa de executar chamadas autenticadas para um nível de backend, como uma base de dados. As contas de serviços AD são comumente utilizadas nestes cenários. Quando você levanta e muda as aplicações para a Azure, a Azure AD DS permite-lhe continuar a usar as contas de serviço da mesma forma. Pode optar por utilizar a mesma conta de serviço que é sincronizada do seu diretório no local para a Azure AD ou criar um OU personalizado e, em seguida, criar uma conta de serviço separada nesse OU. Com qualquer uma das abordagens, as aplicações continuam a funcionar da mesma forma para fazer chamadas autenticadas para outros níveis e serviços.

![Conta de serviço usando WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Neste cenário de exemplo, o Contoso tem uma aplicação de cofre de software personalizada que inclui uma extremidade frontal web, um servidor SQL e um servidor FTP de backend. A autenticação integrada no Windows utilizando contas de serviço autentica a extremidade frontal da web para o servidor FTP. A extremidade frontal da web está configurada para funcionar como uma conta de serviço. O servidor backend está configurado para autorizar o acesso a partir da conta de serviço para a extremidade frontal da web. A Contoso não quer implementar e gerir os seus próprios VMs controladores de domínio na nuvem para mover esta aplicação para o Azure.

Para este cenário, os servidores que hospedam a extremidade frontal da web, o servidor SQL e o servidor FTP podem ser migrados para VMs Azure e unidos a um domínio gerido. Os VMs podem então utilizar a mesma conta de serviço no seu diretório de autenticação no local para efeitos de autenticação da aplicação, que é sincronizada através do Azure AD utilizando o Azure AD Connect.

### <a name="deployment-notes"></a>Notas de implantação

As seguintes considerações de implantação aplicam-se a este caso de utilização de exemplo:

* Certifique-se de que as aplicações utilizam um nome de utilizador e uma palavra-passe para autenticação. A autenticação baseada em certificados ou smartcard não é suportada pela Azure AD DS.
* Não é possível alterar palavras-passe diretamente contra um domínio gerido. Os utilizadores finais podem alterar a sua palavra-passe utilizando [o mecanismo de alteração de senha de autosserviço da Azure AD][sspr] ou contra o diretório no local. Estas alterações são então automaticamente sincronizadas e disponíveis no domínio gerido.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Implementações de serviços de desktop remotos do Windows Server em Azure

Pode utilizar o Azure AD DS para fornecer serviços de domínio geridos a servidores remotos de ambiente de trabalho implantados no Azure.

Para obter mais informações sobre este cenário de implantação, consulte [como integrar os Serviços de Domínio AD Azure com a sua implantação RDS][windows-rds].

## <a name="domain-joined-hdinsight-clusters"></a>Clusters HDInsight unidos ao domínio

Você pode configurar um cluster Azure HDInsight que é unido a um domínio gerido com Apache Ranger habilitado. Você pode criar e aplicar políticas de Colmeia através do Apache Ranger, e permitir que os utilizadores, como cientistas de dados, se conectem à Colmeia usando ferramentas baseadas em ODBC como Excel ou Tableau. Continuamos a trabalhar para adicionar outras cargas de trabalho, tais como HBase, Spark e Storm ao domínio-aderido ao domínio HDInsight.

Para obter mais informações sobre este cenário de implementação, consulte [como configurar clusters HDInsight unidos pelo domínio][hdinsight]

## <a name="next-steps"></a>Passos seguintes

Para começar, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][tutorial-create-instance].

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds