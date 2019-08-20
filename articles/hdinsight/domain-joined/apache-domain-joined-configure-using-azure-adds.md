---
title: Configuração de Enterprise Security Package usando o Azure Active Directory Domain Services-Azure HDInsight
description: Saiba como configurar e configurar um cluster Enterprise Security Package do HDInsight usando o Azure Active Directory Domain Services.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/23/2019
ms.openlocfilehash: 300fd31632a6b3c9043c19dd9b47f40258080261
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614216"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>Configurar um cluster do HDInsight com o Pacote de Segurança Enterprise mediante a utilização do Azure Active Directory Domain Services

Os clusters de Enterprise Security Package (ESP) fornecem acesso de vários usuários em clusters do Azure HDInsight. Os clusters HDInsight com ESP são conectados a um domínio para que os usuários de domínio possam usar suas credenciais de domínio para autenticar com os clusters e executar trabalhos de Big Data.

Neste artigo, você aprenderá a configurar um cluster HDInsight com o ESP usando o Azure Active Directory Domain Services (Azure AD-DS).

> [!NOTE]  
> O ESP está geralmente disponível no HDInsight 3,6 e 4,0 para tipos de cluster: Apache Spark, interativo, Apache Hadoop e HBase. O ESP para Apache Kafka tipo de cluster está em versão prévia.

## <a name="enable-azure-ad-ds"></a>Habilitar Azure AD – DS

> [!NOTE]  
> Somente administradores de locatários têm privilégios para habilitar o Azure AD-DS. Se o armazenamento de cluster for Azure Data Lake Storage (ADLS) Gen1 ou Gen2, você deverá desabilitar a MFA (autenticação multifator) somente para os usuários que precisarem acessar o cluster usando as autenticações Kerberos básicas. Você pode usar [IPs confiáveis](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou [acesso condicional](../../active-directory/conditional-access/overview.md) para desabilitar a MFA para usuários específicos somente quando eles estiverem acessando o intervalo de IP de VNET do cluster HDInsight. Se você estiver usando o acesso condicional, verifique se o ponto de extremidade de serviço do AD está habilitado na VNET do HDInsight.
>
> Se o armazenamento de cluster for o armazenamento de BLOBs do Azure (WASB), não desabilite o MFA.

Habilitar o AzureAD-DS é um pré-requisito para que você possa criar um cluster HDInsight com o ESP. Para obter mais informações, consulte [habilitar Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md). 

Quando o Azure AD-DS está habilitado, todos os usuários e objetos iniciam a sincronização do Azure Active Directory (AAD) para o Azure AD-DS por padrão. O comprimento da operação de sincronização depende do número de objetos no Azure AD. A sincronização pode levar alguns dias para centenas de milhares de objetos. 

O nome de domínio que você usa com o Azure AD-DS deve ter 39 caracteres ou menos para trabalhar com o HDInsight.

Você pode optar por sincronizar apenas os grupos que precisam de acesso aos clusters HDInsight. Essa opção de sincronizar apenas determinados grupos é chamada *sincronização com escopo*. Consulte [Configurar a sincronização com escopo do Azure ad para seu domínio gerenciado](../../active-directory-domain-services/scoped-synchronization.md) para obter instruções.

Ao habilitar o LDAP seguro, coloque o nome de domínio no nome da entidade e o nome alternativo da entidade no certificado. Por exemplo, se o nome de domínio for *contoso100.onmicrosoft.com*, verifique se o nome exato existe no nome da entidade do certificado e no nome alternativo da entidade. Para obter mais informações, consulte [Configurar o LDAP seguro para um domínio gerenciado do Azure AD-DS](../../active-directory-domain-services/tutorial-configure-ldaps.md). Abaixo está um exemplo de como criar um certificado autoassinado e ter o nome de domínio (*contoso100.onmicrosoft.com*) no nome da entidade e DnsName (nome alternativo da entidade):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Verificar o status de integridade do Azure AD-DS
Exiba o status de integridade de sua Azure Active Directory Domain Services selecionando **integridade** na categoria **gerenciar** . Verifique se o status do Azure AD-DS está verde (em execução) e se a sincronização foi concluída.

![Azure Active Directory Domain Services integridade](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Criar e autorizar uma identidade gerenciada

Uma **identidade gerenciada atribuída pelo usuário** é usada para simplificar e proteger as operações dos serviços de domínio. Quando você atribui a função colaborador de serviços de domínio do HDInsight à identidade gerenciada, ela pode ler, criar, modificar e excluir operações de serviços de domínio. Determinadas operações de serviços de domínio, como a criação de UOs e entidades de serviço, são necessárias para o Enterprise Security Package do HDInsight. Identidades gerenciadas podem ser criadas em qualquer assinatura. Para obter mais informações sobre identidades gerenciadas em geral, consulte [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](../hdinsight-managed-identities.md).

Para configurar clusters ESP, crie uma identidade gerenciada atribuída pelo usuário se você ainda não tiver uma. Consulte [criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para obter instruções. Em seguida, atribua a função de **colaborador dos serviços de domínio HDInsight** à identidade gerenciada no controle de acesso do Azure AD-DS (os privilégios de administrador do AAD-DS são necessários para fazer essa atribuição de função).

![Controle de acesso Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

A atribuição da função **colaborador dos serviços de domínio do HDInsight** garante que essa identidade tenha acesso adequado (em nome de) para executar operações de serviços de domínio, como a criação de UOs, a exclusão de UOs, etc. no domínio AAD-DS.

Depois que a identidade gerenciada for criada e receber a função correta, o administrador do AAD-DS poderá configurar quem pode usar essa identidade gerenciada. Para configurar usuários para a identidade gerenciada, o administrador deve selecionar a identidade gerenciada no portal e clicar em **controle de acesso (iam)** em **visão geral**. Em seguida, à direita, atribua a função de **operador de identidade gerenciada** aos usuários ou grupos que desejam criar clusters de ESP do HDInsight. Por exemplo, o administrador do AAD-DS pode atribuir essa função ao grupo **MarketingTeam** para a identidade gerenciada do **sjmsi** , conforme mostrado na imagem a seguir. Isso garantirá que as pessoas certas na organização tenham acesso para usar essa identidade gerenciada com a finalidade de criar clusters ESP.

![Atribuição de função do operador de identidade gerenciada do HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Considerações de redes

> [!NOTE]  
> Azure AD – o DS deve ser implantado em uma vNET baseada em Azure Resource Manager. Não há suporte para redes virtuais clássicas no Azure AD-DS. Veja [habilitar Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md#create-and-configure-the-virtual-network) para obter mais detalhes.

Depois de habilitar o Azure AD-DS, um servidor DNS (serviço de nomes de domínio) local é executado nas VMs (máquinas virtuais) do AD. Configure seu Azure AD-VNET (rede virtual) DS para usar esses servidores DNS personalizados. Para localizar os endereços IP corretos, selecione **Propriedades** na categoria **gerenciar** e examine os endereços IP listados abaixo do **endereço IP na rede virtual**.

![Localizar endereços IP para servidores DNS locais](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Altere a configuração dos servidores DNS na VNET do Azure AD-DS para usar esses IPs personalizados selecionando **servidores DNS** na categoria **configurações** . Em seguida, clique no botão de opção ao lado de **personalizado**, insira o primeiro endereço IP na caixa de texto abaixo e clique em **salvar**. Adicione outros endereços IP usando as mesmas etapas.

![Atualizando a configuração de DNS da VNET](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

É mais fácil posicionar a instância do Azure AD-DS e o cluster HDInsight na mesma rede virtual do Azure. Se você planeja usar diferentes VNETs, você deve emparelhar essas redes virtuais para que o controlador de domínio fique visível para VMs HDI. Para obter mais informações, consulte [emparelhamento de rede virtual](../../virtual-network/virtual-network-peering-overview.md). 

Depois que os VNETs estiverem emparelhados, configure a VNET do HDInsight para usar um servidor DNS personalizado e insira os IPs privados do Azure AD-DS como os endereços do servidor DNS. Quando ambos os VNETs usam os mesmos servidores DNS, seu nome de domínio personalizado será resolvido para o IP correto e poderá ser acessado do HDInsight. Por exemplo, se o nome de `contoso.com` domínio for depois dessa etapa `ping contoso.com` , o deverá ser resolvido para o IP do Azure AD-DS correto.

![Configurando servidores DNS personalizados para VNET emparelhadas](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se você estiver usando regras de NSG (grupos de segurança de rede) em sua sub-rede do HDInsight, deverá permitir os [IPS necessários](../hdinsight-management-ip-addresses.md) para o tráfego de entrada e de saída. 

**Para testar** se a rede está configurada corretamente, ingresse em uma VM do Windows na VNET/sub-rede do HDInsight e execute o ping no nome de domínio (ele deve ser resolvido para um IP) e, em seguida, clique em **LDP. exe** para acessar o domínio do Azure AD-DS. Em seguida, **ingresse essa VM do Windows no domínio para confirmar** que todas as chamadas RPC necessárias são bem sucedidos entre o cliente e o servidor. Você também pode usar o **nslookup** para confirmar o acesso de rede à sua conta de armazenamento ou a qualquer BD externo que você possa usar (por exemplo, External metastore do hive ou Ranger DB).
Você deve certificar-se de que todas as [portas necessárias](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) estejam na lista de permissões nas regras do grupo de segurança de rede de sub-rede do AAD-DS, se o AAD-DS estiver protegido por um NSG. Se o domínio que ingressar nessa VM do Windows for bem-sucedido, você poderá prosseguir para a próxima etapa e criar clusters ESP.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Criar um cluster HDInsight com ESP

Depois de configurar as etapas anteriores corretamente, a próxima etapa é criar o cluster HDInsight com o ESP habilitado. Ao criar um cluster HDInsight, você pode habilitar Enterprise Security Package na guia **personalizado** . Se você preferir usar um modelo de Azure Resource Manager para implantação, use a experiência do portal uma vez e baixe o modelo de preenchimento prévio na última página de "Resumo" para reutilização futura.

> [!NOTE]  
> Os seis primeiros caracteres dos nomes de cluster do ESP devem ser exclusivos em seu ambiente. Por exemplo, se você tiver vários clusters ESP em diferentes VNETs, deverá escolher um convension de nomenclatura que garanta que os seis primeiros caracteres nos nomes de cluster sejam exclusivos.

![Validação de domínio do pacote de segurança do Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Depois de habilitar o ESP, as configurações incorretas comuns relacionadas ao Azure AD-DS serão detectadas e validadas automaticamente. Depois de corrigir esses erros, você pode prosseguir com a próxima etapa: 

![Falha na validação de domínio do pacote de segurança do Azure HDInsight Enterprise](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

Ao criar um cluster HDInsight com o ESP, você deve fornecer os seguintes parâmetros:

- **Usuário administrador do cluster**: Escolha um administrador para o cluster do Azure AD-DS sincronizado. Essa conta de domínio já deve estar sincronizada e disponível no Azure AD-DS.

- **Grupos de acesso ao cluster**: Os grupos de segurança cujos usuários você deseja sincronizar e que têm acesso ao cluster devem estar disponíveis no Azure AD-DS. Por exemplo, HiveUsers Group. Para obter mais informações, consulte [criar um grupo e adicionar membros em Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **URL**DE LDAPS: Um exemplo é `ldaps://contoso.com:636`.

A captura de tela a seguir mostra uma configuração bem-sucedida no portal do Azure:

![Configuração de Active Directory Domain Services do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

A identidade gerenciada que você criou pode ser escolhida no menu suspenso identidade gerenciada atribuída pelo usuário ao criar um novo cluster.

![Configuração de Active Directory Domain Services do Azure HDInsight ESP](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).

## <a name="next-steps"></a>Passos Seguintes

* Para configurar políticas do hive e executar consultas do hive, consulte [Configurar políticas de Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para usar o SSH para se conectar a clusters HDInsight com o ESP, consulte [usar SSH com Apache Hadoop baseado em Linux no HDInsight do Linux, UNIX ou os X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
