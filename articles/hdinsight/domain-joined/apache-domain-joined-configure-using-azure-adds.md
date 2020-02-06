---
title: Segurança corporativa com o Azure AD DS – Azure HDInsight
description: Saiba como configurar e configurar um cluster Enterprise Security Package do HDInsight usando o Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: a3a23e2adb60b2b0fd5f4a5e790990ce2ad85fa9
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031176"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Enterprise Security Package configurações com Azure Active Directory Domain Services no HDInsight

Os clusters de Enterprise Security Package (ESP) fornecem acesso multiusuário em clusters do Azure HDInsight. Os clusters HDInsight com ESP são conectados a um domínio para que os usuários de domínio possam usar suas credenciais de domínio para autenticar com os clusters e executar trabalhos de Big Data.

Neste artigo, você aprenderá a configurar um cluster HDInsight com o ESP usando o Azure Active Directory Domain Services (AD DS do Azure).

> [!NOTE]  
> O ESP está geralmente disponível no HDInsight 3,6 e 4,0 para esses tipos de cluster: Apache Spark, Interactive, Hadoop e HBase. O ESP para o tipo de cluster Apache Kafka está em versão prévia com suporte de melhor esforço. Os clusters ESP criados antes da data de GA do ESP (1 de outubro de 2018) não têm suporte.

## <a name="enable-azure-ad-ds"></a>Habilitar AD DS do Azure

> [!NOTE]  
> Somente administradores de locatários têm privilégios para habilitar o Azure AD DS. Se o armazenamento de cluster for Azure Data Lake Storage Gen1 ou Gen2, você deverá desabilitar a autenticação multifator do Azure somente para usuários que precisarão acessar o cluster usando a autenticação Kerberos básica. 
>
> Só é possível utilizar [IPs fidedignos](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou [acesso condicional](../../active-directory/conditional-access/overview.md) para desativar a autenticação multi-factor para utilizadores específicos *apenas* quando estiverem a aceder à gama IP para a rede virtual do cluster HDInsight. Se você estiver usando o acesso condicional, certifique-se de que o ponto de extremidade de serviço do Active Directory em habilitado na rede virtual do HDInsight.
>
> Se o armazenamento de cluster for o armazenamento de BLOBs do Azure, não desabilite a autenticação multifator.

Habilitar o Azure AD DS é um pré-requisito para que você possa criar um cluster HDInsight com o ESP. Para mais informações, consulte [enable Azure Ative Directory Domain Services utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance.md). 

Quando o AD DS do Azure é habilitado, todos os usuários e objetos iniciam a sincronização do Azure Active Directory (Azure AD) para o Azure AD DS por padrão. O comprimento da operação de sincronização depende do número de objetos no Azure AD. A sincronização pode levar alguns dias para centenas de milhares de objetos. 

O nome de domínio que você usa com o Azure AD DS deve ter 39 caracteres ou menos, para trabalhar com o HDInsight.

Você pode optar por sincronizar apenas os grupos que precisam de acesso aos clusters HDInsight. Esta opção de sincronizar apenas determinados grupos chama-se *sincronização por âmbito.* Para obter instruções, consulte a [sincronização de configuração de 'AD' do Azure para o seu domínio gerido](../../active-directory-domain-services/scoped-synchronization.md).

Quando você estiver habilitando o LDAP seguro, coloque o nome de domínio no nome da entidade e o nome alternativo da entidade no certificado. Por exemplo, se o seu nome de domínio for *contoso100.onmicrosoft.com,* certifique-se de que o nome exato existe no nome do seu certificado e no nome alternativo do sujeito. Para obter mais informações, consulte [Configure secure LDAP para um domínio gerido por DS Azure AD](../../active-directory-domain-services/tutorial-configure-ldaps.md). 

O exemplo a seguir cria um certificado autoassinado. O nome de domínio *contoso100.onmicrosoft.com* está em ambos os `Subject` (nome do assunto) e `DnsName` (nome alternativo do sujeito).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Verificar o status de integridade do Azure AD DS
Veja o estado de saúde dos Serviços de Domínio de Diretório Ativo Azure selecionando **a Saúde** na categoria **Gerir.** Verifique se o status da AD DS do Azure está verde (em execução) e se a sincronização está concluída.

![Integridade de AD DS do Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Criar e autorizar uma identidade gerenciada

Pode utilizar uma *identidade gerida atribuída* ao utilizador para simplificar e ajudar a garantir operações de serviços de domínio. Ao atribuir a função de Colaborador de Serviços de **Domínio HDInsight** à identidade gerida, pode ler, criar, modificar e eliminar operações de serviços de domínio. 

Determinadas operações de serviços de domínio, como a criação de UOs e entidades de serviço, são necessárias para o Enterprise Security Package do HDInsight. Você pode criar identidades gerenciadas em qualquer assinatura. Para obter mais informações sobre identidades geridas em geral, consulte [identidades geridas para os recursos Do Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Para obter mais informações sobre como as identidades geridas funcionam no Azure HDInsight, consulte [identidades geridas no Azure HDInsight](../hdinsight-managed-identities.md).

Para configurar clusters ESP, crie uma identidade gerenciada atribuída pelo usuário se você ainda não tiver uma. Para instruções, consulte [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)pelo utilizador utilizando o portal Azure . 

Em seguida, atribuir o papel de Colaborador de Serviços de **Domínio HDInsight** à identidade gerida no controlo de **acesso** para Azure AD DS. Você precisa de privilégios de administrador de AD DS do Azure para fazer essa atribuição de função.

![Controle de acesso Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Atribuir a função de Colaborador de Serviços de **Domínio HDInsight** garante que esta identidade tem acesso adequado (em nome de) para realizar operações de serviços de domínio no domínio Azure AD DS. Essas operações incluem a criação e a exclusão de UOs.

Depois que a identidade gerenciada é criada e recebe a função correta, o administrador de AD DS do Azure pode configurar quem pode usar essa identidade gerenciada. Primeiro, o administrador seleciona a identidade gerida no portal e, em seguida, seleciona o Controlo de **Acesso (IAM)** sob **visão geral**. Em seguida, à direita, o administrador atribui a função de **Operador de Identidade Gerida** aos utilizadores ou grupos que pretendem criar clusters ESP HDInsight. 

Por exemplo, o administrador azure AD DS pode atribuir esta função ao grupo **MarketingTeam** para a identidade gerida pelo **SJMSI,** como mostra a seguinte imagem. Essa atribuição garante que as pessoas certas na organização possam usar a identidade gerenciada para criar clusters ESP.

![Atribuição de função do operador de identidade gerenciada do HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Considerações de rede

> [!NOTE]  
> O Azure AD DS deve ser implantado em uma rede virtual baseada em Azure Resource Manager. Não há suporte para redes virtuais clássicas no Azure AD DS. Para mais informações, consulte [enable Azure Ative Directory Domain Services utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Depois de habilitar o Azure AD DS, um servidor DNS (sistema de nomes de domínio) local é executado no Active Directory VMs (máquinas virtuais). Configure sua rede virtual do Azure AD DS para usar esses servidores DNS personalizados. Para localizar os endereços IP certos, selecione **Propriedades** na categoria **Gerir** e procure em **IP ADDRESS NA REDE VIRTUAL**.

![Localizar endereços IP para servidores DNS locais](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Altere a configuração dos servidores DNS na rede virtual Azure AD DS para utilizar estes IPs personalizados selecionando **servidores DNS** na categoria **Definições.** Em seguida, selecione a opção **Custom,** introduza o primeiro endereço IP na caixa de texto e selecione **Guardar**. Adicione mais endereços IP usando as mesmas etapas.

![Atualizando a configuração de DNS da rede virtual](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

É mais fácil posicionar a instância do AD DS do Azure e o cluster HDInsight na mesma rede virtual do Azure. Se você planeja usar redes virtuais diferentes, você deve emparelhar essas redes virtuais para que o controlador de domínio fique visível para VMs do HDInsight. Para mais informações, consulte o peering da [rede Virtual.](../../virtual-network/virtual-network-peering-overview.md) 

Depois que as redes virtuais estiverem emparelhadas, configure a rede virtual do HDInsight para usar um servidor DNS personalizado e insira os IPs privados do Azure AD DS como os endereços do servidor DNS. Quando ambas as redes virtuais usam os mesmos servidores DNS, seu nome de domínio personalizado será resolvido para o IP correto e poderá ser acessado do HDInsight. Por exemplo, se o seu nome de domínio for `contoso.com`, então depois deste passo, `ping contoso.com` deve resolver para o IP AD DS Azure direito.

![Configurando servidores DNS personalizados para uma rede virtual emparelhada](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se estiver a utilizar as regras do grupo de segurança de rede (NSG) na sua subnet HDInsight, deverá permitir os [IPs necessários](../hdinsight-management-ip-addresses.md) tanto para o tráfego de entrada como para a saída.

Para testar se a rede está configurada corretamente, ingresse em uma VM do Windows na rede virtual/sub-rede do HDInsight e execute o ping no nome de domínio. (Deve ser resolvido a um IP.) Executar **ldp.exe** para aceder ao domínio Azure AD DS. Em seguida, ingresse essa VM do Windows no domínio para confirmar que todas as chamadas RPC necessárias são bem sucedidos entre o cliente e o servidor. 

Também pode utilizar o **nslookup** para confirmar o acesso à rede à sua conta de armazenamento ou a qualquer base de dados externa que possa utilizar (por exemplo, metaloja externa da Hive ou Ranger DB). Certifique-se de que todas as [portas necessárias](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) são permitidas nas regras NSG da sub-rede Azure AD DS, se um NSG ajudar a assegurar o Azure AD DS. Se o domínio que ingressar nessa VM do Windows for bem-sucedido, você poderá continuar na próxima etapa e criar clusters ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Criar um cluster HDInsight com ESP

Depois de configurar as etapas anteriores corretamente, a próxima etapa é criar o cluster HDInsight com o ESP habilitado. Ao criar um cluster HDInsight, pode ativar o Pacote de Segurança empresarial no separador **de rede Security +.** Se preferir utilizar um modelo de Gestor de Recursos Azure para implementação, use a experiência do portal uma vez e descarregue o modelo pré-preenchido no **Review + crie** a página para futura reutilização. 

Também pode ativar a funcionalidade [HDInsight ID Broker](identity-broker.md) durante a criação do cluster. O recurso de agente de ID permite que você entre no Ambari usando a autenticação multifator e obtenha os tíquetes Kerberos necessários sem precisar de hashes de senha no Azure AD DS.

> [!NOTE]  
> Os seis primeiros caracteres dos nomes de cluster do ESP devem ser exclusivos em seu ambiente. Por exemplo, se você tiver vários clusters ESP em redes virtuais diferentes, escolha uma Convenção de nomenclatura que garanta que os primeiros seis caracteres nos nomes de cluster sejam exclusivos.

![Validação de domínio para o Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Depois de habilitar o ESP, as configurações incorretas comuns relacionadas à AD DS do Azure são detectadas e validadas automaticamente. Depois de corrigir esses erros, você pode continuar com a próxima etapa.

![Falha na validação de domínio do Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Ao criar um cluster HDInsight com o ESP, você deve fornecer os seguintes parâmetros:

- **Utilizador administrativo**do cluster : Escolha um administrador para o seu cluster a partir da sua instância AD DS Azure sincronizada. Essa conta de domínio já deve estar sincronizada e disponível no Azure AD DS.

- **Grupos**de acesso ao cluster : Os grupos de segurança cujos utilizadores deseja sincronizar e ter acesso ao cluster devem estar disponíveis no Azure AD DS. Um exemplo é o grupo HiveUsers. Para mais informações, consulte [Criar um grupo e adicionar membros no Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL LDAPS**: Um exemplo é `ldaps://contoso.com:636`.

A identidade gerida que criou pode ser escolhida a partir da lista de abandono de **identidade gerida pelo Utilizador** quando estiver a criar um novo cluster.

![Identidade gerenciada Active Directory Domain Services do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Passos seguintes

* Para configurar as políticas da Colmeia e executar consultas de Hive, consulte as políticas de [Configure Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para utilizar o SSH para se ligar aos clusters HDInsight com ESP, consulte [Use SSH com Apache Hadoop baseado em Linux no HDInsight a partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
