---
title: Segurança corporativa com o Azure AD DS – Azure HDInsight
description: Saiba como configurar e configurar um cluster Enterprise Security Package do HDInsight usando o Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 10/02/2019
ms.openlocfilehash: 01de2173ee5d55f24f97e1d057e96d93a56c2af0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435978"
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
> Você pode usar [IPs confiáveis](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou [acesso condicional](../../active-directory/conditional-access/overview.md) para desabilitar a autenticação multifator para usuários específicos *somente* quando eles estiverem acessando o intervalo de IP para a rede virtual do cluster HDInsight. Se você estiver usando o acesso condicional, certifique-se de que o ponto de extremidade de serviço do Active Directory em habilitado na rede virtual do HDInsight.
>
> Se o armazenamento de cluster for o armazenamento de BLOBs do Azure, não desabilite a autenticação multifator.

Habilitar o Azure AD DS é um pré-requisito para que você possa criar um cluster HDInsight com o ESP. Para obter mais informações, consulte [habilitar Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md). 

Quando o AD DS do Azure é habilitado, todos os usuários e objetos iniciam a sincronização do Azure Active Directory (Azure AD) para o Azure AD DS por padrão. O comprimento da operação de sincronização depende do número de objetos no Azure AD. A sincronização pode levar alguns dias para centenas de milhares de objetos. 

O nome de domínio que você usa com o Azure AD DS deve ter 39 caracteres ou menos, para trabalhar com o HDInsight.

Você pode optar por sincronizar apenas os grupos que precisam de acesso aos clusters HDInsight. Essa opção de sincronizar apenas determinados grupos é chamada *sincronização com escopo*. Para obter instruções, consulte [Configurar a sincronização com escopo do Azure ad para seu domínio gerenciado](../../active-directory-domain-services/scoped-synchronization.md).

Quando você estiver habilitando o LDAP seguro, coloque o nome de domínio no nome da entidade e o nome alternativo da entidade no certificado. Por exemplo, se o nome de domínio for *contoso100.onmicrosoft.com*, verifique se o nome exato existe no nome da entidade do certificado e no nome alternativo da entidade. Para obter mais informações, consulte [Configurar o LDAP seguro para um domínio gerenciado do Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md). 

O exemplo a seguir cria um certificado autoassinado. O nome de domínio *contoso100.onmicrosoft.com* está em ambos `Subject` (nome da entidade) e `DnsName` (nome alternativo da entidade).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Verificar o status de integridade do Azure AD DS
Exiba o status de integridade de Azure Active Directory Domain Services selecionando **integridade** na categoria **gerenciar** . Verifique se o status da AD DS do Azure está verde (em execução) e se a sincronização está concluída.

![Integridade de AD DS do Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Criar e autorizar uma identidade gerenciada

Você pode usar uma *identidade gerenciada atribuída pelo usuário* para simplificar e ajudar a proteger as operações dos serviços de domínio. Quando você atribui a função **colaborador de serviços de domínio do HDInsight** à identidade gerenciada, ela pode ler, criar, modificar e excluir operações de serviços de domínio. 

Determinadas operações de serviços de domínio, como a criação de UOs e entidades de serviço, são necessárias para o Enterprise Security Package do HDInsight. Você pode criar identidades gerenciadas em qualquer assinatura. Para obter mais informações sobre identidades gerenciadas em geral, consulte [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](../hdinsight-managed-identities.md).

Para configurar clusters ESP, crie uma identidade gerenciada atribuída pelo usuário se você ainda não tiver uma. Para obter instruções, consulte [criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Em seguida, atribua a função **colaborador dos serviços de domínio do HDInsight** à identidade gerenciada no **controle de acesso** para AD DS do Azure. Você precisa de privilégios de administrador de AD DS do Azure para fazer essa atribuição de função.

![Controle de acesso Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

A atribuição da função **colaborador dos serviços de domínio HDInsight** garante que essa identidade tenha acesso adequado (em nome de) para executar operações de serviços de domínio no domínio AD DS do Azure. Essas operações incluem a criação e a exclusão de UOs.

Depois que a identidade gerenciada é criada e recebe a função correta, o administrador de AD DS do Azure pode configurar quem pode usar essa identidade gerenciada. Primeiro, o administrador seleciona a identidade gerenciada no portal e, em seguida, seleciona o **controle de acesso (iam)** em **visão geral**. Em seguida, à direita, o administrador atribui a função de **operador de identidade gerenciada** aos usuários ou grupos que desejam criar clusters de ESP do HDInsight. 

Por exemplo, o administrador de AD DS do Azure pode atribuir essa função ao grupo **MarketingTeam** para a identidade gerenciada **sjmsi** , conforme mostrado na imagem a seguir. Essa atribuição garante que as pessoas certas na organização possam usar a identidade gerenciada para criar clusters ESP.

![Atribuição de função do operador de identidade gerenciada do HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Considerações de rede

> [!NOTE]  
> O Azure AD DS deve ser implantado em uma rede virtual baseada em Azure Resource Manager. Não há suporte para redes virtuais clássicas no Azure AD DS. Para obter mais informações, consulte [habilitar Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Depois de habilitar o Azure AD DS, um servidor DNS (sistema de nomes de domínio) local é executado no Active Directory VMs (máquinas virtuais). Configure sua rede virtual do Azure AD DS para usar esses servidores DNS personalizados. Para localizar os endereços IP corretos, selecione **Propriedades** na categoria **gerenciar** e procure em **endereço IP na rede virtual**.

![Localizar endereços IP para servidores DNS locais](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Altere a configuração dos servidores DNS na rede virtual do Azure AD DS para usar esses IPs personalizados selecionando **servidores DNS** na categoria **configurações** . Em seguida, selecione a opção **personalizado** , insira o primeiro endereço IP na caixa de texto e selecione **salvar**. Adicione mais endereços IP usando as mesmas etapas.

![Atualizando a configuração de DNS da rede virtual](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

É mais fácil posicionar a instância do AD DS do Azure e o cluster HDInsight na mesma rede virtual do Azure. Se você planeja usar redes virtuais diferentes, você deve emparelhar essas redes virtuais para que o controlador de domínio fique visível para VMs do HDInsight. Para obter mais informações, consulte [emparelhamento de rede virtual](../../virtual-network/virtual-network-peering-overview.md). 

Depois que as redes virtuais estiverem emparelhadas, configure a rede virtual do HDInsight para usar um servidor DNS personalizado e insira os IPs privados do Azure AD DS como os endereços do servidor DNS. Quando ambas as redes virtuais usam os mesmos servidores DNS, seu nome de domínio personalizado será resolvido para o IP correto e poderá ser acessado do HDInsight. Por exemplo, se o nome de domínio for `contoso.com`, após essa etapa, `ping contoso.com` deverá ser resolvido para o IP do Azure AD DS correto.

![Configurando servidores DNS personalizados para uma rede virtual emparelhada](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se você estiver usando regras de NSG (grupo de segurança de rede) em sua sub-rede do HDInsight, deverá permitir os [IPS necessários](../hdinsight-management-ip-addresses.md) para o tráfego de entrada e de saída.

Para testar se a rede está configurada corretamente, ingresse em uma VM do Windows na rede virtual/sub-rede do HDInsight e execute o ping no nome de domínio. (Ele deve ser resolvido para um IP.) Execute o **LDP. exe** para acessar o domínio de AD DS do Azure. Em seguida, ingresse essa VM do Windows no domínio para confirmar que todas as chamadas RPC necessárias são bem sucedidos entre o cliente e o servidor. 

Você também pode usar **nslookup** para confirmar o acesso à rede para sua conta de armazenamento ou qualquer banco de dados externo que você possa usar (por exemplo, metastore do hive externo ou o Ranger DB). Certifique-se de que todas as [portas necessárias](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) sejam permitidas nas regras NSG da sub-rede do Azure AD DS, se uma NSG ajudar a proteger o AD DS do Azure. Se o domínio que ingressar nessa VM do Windows for bem-sucedido, você poderá continuar na próxima etapa e criar clusters ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Criar um cluster HDInsight com ESP

Depois de configurar as etapas anteriores corretamente, a próxima etapa é criar o cluster HDInsight com o ESP habilitado. Ao criar um cluster HDInsight, você pode habilitar Enterprise Security Package na guia **segurança + rede** . Se você preferir usar um modelo de Azure Resource Manager para implantação, use a experiência do portal uma vez e baixe o modelo preenchedo na página **revisar + criar** para reutilização futura. 

Você também pode habilitar o recurso [agente de ID do HDInsight](identity-broker.md) durante a criação do cluster. O recurso de agente de ID permite que você entre no Ambari usando a autenticação multifator e obtenha os tíquetes Kerberos necessários sem precisar de hashes de senha no Azure AD DS.

> [!NOTE]  
> Os seis primeiros caracteres dos nomes de cluster do ESP devem ser exclusivos em seu ambiente. Por exemplo, se você tiver vários clusters ESP em redes virtuais diferentes, escolha uma Convenção de nomenclatura que garanta que os primeiros seis caracteres nos nomes de cluster sejam exclusivos.

![Validação de domínio para o Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Depois de habilitar o ESP, as configurações incorretas comuns relacionadas à AD DS do Azure são detectadas e validadas automaticamente. Depois de corrigir esses erros, você pode continuar com a próxima etapa.

![Falha na validação de domínio do Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Ao criar um cluster HDInsight com o ESP, você deve fornecer os seguintes parâmetros:

- **Usuário administrador do cluster**: escolha um administrador para o cluster da instância do Azure AD DS sincronizada. Essa conta de domínio já deve estar sincronizada e disponível no Azure AD DS.

- **Grupos de acesso de cluster**: os grupos de segurança cujos usuários você deseja sincronizar e que têm acesso ao cluster devem estar disponíveis no Azure AD DS. Um exemplo é o grupo HiveUsers. Para obter mais informações, consulte [criar um grupo e adicionar membros em Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL de LDAPS**: um exemplo é `ldaps://contoso.com:636`.

A identidade gerenciada que você criou pode ser escolhida na lista suspensa **identidade gerenciada atribuída pelo usuário** quando você está criando um novo cluster.

![Identidade gerenciada Active Directory Domain Services do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Passos seguintes

* Para configurar políticas do hive e executar consultas do hive, consulte [Configurar políticas de Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para usar o SSH para se conectar a clusters HDInsight com o ESP, consulte [usar SSH com Apache Hadoop baseado em Linux no HDInsight do Linux, UNIX ou os X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
