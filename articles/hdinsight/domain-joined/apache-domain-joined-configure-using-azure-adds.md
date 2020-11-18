---
title: Configurar clusters para integração de diretórios ativos
titleSuffix: Azure HDInsight
description: Aprenda a configurar e configurar um cluster HDInsight integrado com o Ative Directory utilizando os Serviços de Domínio do Diretório Ativo Azure e a funcionalidade Pacote de Segurança Empresarial.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020, contperfq2
ms.date: 10/30/2020
ms.openlocfilehash: 4c0d12e4c37476b9ae71962251105ef92aa39120
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845208"
---
# <a name="configure-hdinsight-clusters-for-active-directory-integration-with-enterprise-security-package"></a>Configurar clusters HDInsight para integração de diretório ativo com pacote de segurança empresarial

Neste artigo, aprende-se a criar e configurar um cluster HDInsight integrado com o Ative Directory utilizando uma funcionalidade chamada Enterprise Security Package (ESP), Azure Ative Directory Domain Services (Azure AD-DS) e o seu Ative Directory pré-existente no local.

Para um tutorial sobre a configuração e configuração de um domínio em Azure e a criação de um cluster habilitado para o E, consulte [criar e configurar clusters de pacotes de segurança empresarial em Azure HDInsight](apache-domain-joined-create-configure-enterprise-security-cluster.md).

## <a name="background"></a>Fundo

O Pacote de Segurança Empresarial (ESP) proporciona integração de Diretório Ativo para a Azure HDInsight. Esta integração permite que os utilizadores de domínio utilizem as suas credenciais de domínio para autenticar com clusters HDInsight e executar grandes trabalhos de dados.

> [!NOTE]  
> O ESP está geralmente disponível em HDInsight 3.6 e 4.0 para estes tipos de cluster: Apache Spark, Interactive, Hadoop e HBase. ESP para o tipo de cluster Apache Kafka está em pré-visualização apenas com o melhor suporte de esforço. Os clusters ESP criados antes da data do ESP GA (1 de outubro de 2018) não são suportados.

## <a name="prerequisites"></a>Pré-requisitos

Existem alguns pré-requisitos para completar antes de poder criar um cluster HDInsight habilitado a EST:

- Ativar a ad-ds azure.
- Verifique o estado de saúde do Azure AD-DS para garantir a sincronização concluída.
- Criar e autorizar uma identidade gerida.
- Configuração completa de rede para DNS e questões relacionadas.

Cada um destes itens será discutido em detalhe abaixo.

### <a name="enable-azure-ad-ds"></a>Ativar o Azure AD DS

Permitir que o Azure AD DS seja um pré-requisito antes de poder criar um cluster HDInsight com ESP. Para obter mais informações, consulte [Enable Azure Ative Directory Domain Services utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance.md).

Quando o Azure AD DS está ativado, todos os utilizadores e objetos começam a sincronizar de Azure Ative Directory (Azure AD) para Azure AD DS por padrão. O comprimento da operação de sincronização depende do número de objetos em Azure AD. A sincronização pode levar alguns dias para centenas de milhares de objetos.

O nome de domínio que utiliza com Azure AD DS deve ter 39 caracteres ou menos, para trabalhar com HDInsight.

Pode optar por sincronizar apenas os grupos que precisam de acesso aos clusters HDInsight. Esta opção de sincronização de apenas certos grupos é chamada *de sincronização âmbito*. Para obter instruções, consulte a [sincronização de configure scoped da AD AZure para o seu domínio gerido](../../active-directory-domain-services/scoped-synchronization.md).

Quando estiver a ativar lDAP seguro, coloque o nome de domínio no nome do assunto. E o nome alternativo do sujeito no certificado. Se o seu nome de domínio for *contoso100.onmicrosoft.com,* certifique-se de que o nome exato existe no nome do seu certificado e nome alternativo sujeito. Para obter mais informações, consulte [o Configure Secure LDAP para um domínio gerido AZure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md).

O exemplo a seguir cria um certificado auto-assinado. O nome de domínio *contoso100.onmicrosoft.com* está em ambos `Subject` (nome do sujeito) e `DnsName` (nome alternativo sujeito).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

> [!NOTE]  
> Apenas os administradores de inquilinos têm os privilégios de permitir a Azure AD DS. Se o armazenamento do cluster for Azure Data Lake Storage Gen1 ou Gen2, você deve desativar a autenticação multi-factor Azure AD apenas para utilizadores que precisarão de aceder ao cluster usando a autenticação básica de Kerberos.
>
> Pode utilizar [IPs fidedignos](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou [Acesso Condicional](../../active-directory/conditional-access/overview.md) para desativar a autenticação multi-factor para utilizadores específicos *apenas* quando estiverem a aceder à gama IP para a rede virtual do cluster HDInsight. Se estiver a utilizar o Acesso Condicional, certifique-se de que o ponto de terminação do serviço Ative Directory está ativado na rede virtual HDInsight.
>
> Se o armazenamento do cluster for o armazenamento Azure Blob, não desative a autenticação multi-factor.

### <a name="check-azure-ad-ds-health-status"></a>Verifique o estado de saúde da Azure AD DS

Consulte o estado de saúde dos Serviços de Domínio do Diretório Ativo Azure selecionando **Saúde** na categoria **Gerir.** Certifique-se de que o estado do Azure AD DS é verde (em funcionamento) e que a sincronização está completa.

![Saúde da AD DS Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

### <a name="create-and-authorize-a-managed-identity"></a>Criar e autorizar uma identidade gerida

Utilize uma *identidade gerida atribuída pelo utilizador* para simplificar as operações de serviços de domínio seguros. Quando atribui a função **de Contribuinte de Serviços de Domínio HDInsight** à identidade gerida, pode ler, criar, modificar e eliminar operações de serviços de domínio.

Certas operações de serviços de domínio, tais como a criação de OUs e principais serviços, são necessárias para o Pacote de Segurança Empresarial HDInsight. Pode criar identidades geridas em qualquer subscrição. Para obter mais informações sobre identidades geridas em geral, consulte [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como as identidades geridas funcionam em Azure HDInsight, consulte [identidades geridas em Azure HDInsight](../hdinsight-managed-identities.md).

Para configurar clusters ESP, crie uma identidade gerida atribuída ao utilizador se ainda não o tiver. [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)Ver.

Em seguida, atribua o papel **de Contribuinte de Serviços de Domínio HDInsight** à identidade gerida no **controlo access** para Azure AD DS. Precisa de privilégios administrativos da Azure AD DS para fazer esta tarefa.

![Controlo de acesso a serviços de domínio do diretório ativo Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

A atribuição da função **hdInsight Domain Services Contributor** garante que esta identidade tem acesso adequado `on behalf of` () para fazer operações de serviços de domínio no domínio Azure AD DS. Estas operações incluem a criação e eliminação de OUs.

Após a identidade gerida ser dada o papel, o administrador Azure AD DS gere quem a utiliza. Primeiro, o administrador seleciona a identidade gerida no portal. Em seguida, selecione **Access Control (IAM)** sob **visão geral**. O administrador atribui a função **de Operador de Identidade Gerida** a utilizadores ou grupos que pretendam criar clusters ESP.

Por exemplo, o administrador Azure AD DS pode atribuir este papel ao grupo **MarketingTeam** para a identidade gerida **sjmsi.** Um exemplo é mostrado na imagem seguinte. Esta atribuição garante que as pessoas certas na organização podem usar a identidade gerida para criar clusters ESP.

![Atribuição de função de operador de identidade gerida por HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

### <a name="network-configuration"></a>Configuração de rede

> [!NOTE]  
> O Azure AD DS deve ser implantado numa rede virtual baseada no Azure Resource Manager. As redes virtuais clássicas não são suportadas pela Azure AD DS. Para obter mais informações, consulte [Enable Azure Ative Directory Domain Services utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Ativar a ad ds Azure. Em seguida, um servidor local do Sistema de Nome de Domínio (DNS) funciona nas máquinas virtuais do Ative Directory (VMs). Configure a sua rede virtual Azure AD DS para utilizar estes servidores DNS personalizados. Para localizar os endereços IP certos, selecione **Propriedades** na categoria **Gerir** e procure em **ENDEREÇO IP NA REDE VIRTUAL**.

![Localizar endereços IP para servidores DNS locais](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Altere a configuração dos servidores DNS na rede virtual Azure AD DS. Para utilizar estes IPs personalizados, selecione **servidores DNS** na categoria **Definições.** Em seguida, selecione a opção **'Personalizar',** introduza o primeiro endereço IP na caixa de texto e selecione **Guardar**. Adicione mais endereços IP utilizando os mesmos passos.

![Atualizar a configuração de DNS de rede virtual](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

É mais fácil colocar tanto a instância AD DS do Azure como o cluster HDInsight na mesma rede virtual Azure. Se planeia utilizar diferentes redes virtuais, deve espreitar essas redes virtuais para que o controlador de domínio seja visível para os VMs HDInsight. Para obter mais informações, consulte [a rede Virtual a espreitar.](../../virtual-network/virtual-network-peering-overview.md)

Depois de as redes virtuais serem espreitadas, configurar a rede virtual HDInsight para utilizar um servidor DNS personalizado. E insira os IPs privados Azure AD DS como endereços do servidor DNS. Quando ambas as redes virtuais utilizarem os mesmos servidores DNS, o nome de domínio personalizado resolverá para o IP certo e será acessível a partir de HDInsight. Por exemplo, se o seu nome de domínio for `contoso.com` , então após este passo, `ping contoso.com` deve resolver-se para o Azure AD DS IP direito.

![Configurar servidores DNS personalizados para uma rede virtual esprevada](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se estiver a utilizar as regras do grupo de segurança da rede (NSG) na sua sub-rede HDInsight, deve permitir os [IPs necessários](../hdinsight-management-ip-addresses.md) tanto para o tráfego de entrada como para a saída.

Para testar a configuração da sua rede, junte-se a um VM do Windows à rede virtual/sub-rede HDInsight e pingue o nome de domínio. (Deve resolver-se para um IP.) Executar **ldp.exe** para aceder ao domínio Azure AD DS. Em seguida, junte-se a este Windows VM ao domínio para confirmar que todas as chamadas RPC necessárias têm sucesso entre o cliente e o servidor.

Utilize **o nslookup** para confirmar o acesso à rede à sua conta de armazenamento. Ou qualquer base de dados externa que possa utilizar (por exemplo, metásta de Colmeia externa ou Ranger DB). Certifique-se de que as [portas necessárias](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) são permitidas nas regras NSG da sub-rede Azure AD DS, se um NSG garantir Azure AD DS. Se a adesão de domínio deste Windows VM for bem sucedida, então pode continuar até ao passo seguinte e criar clusters ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Criar um cluster HDInsight com ESP

Depois de configurar corretamente os passos anteriores, o próximo passo é criar o cluster HDInsight com ESP ativado. Quando criar um cluster HDInsight, pode ativar o Pacote de Segurança Empresarial no separador **Segurança + rede.** Para um modelo de Gestor de Recursos Azure para implantação, utilize a experiência do portal uma vez. Em seguida, descarregue o modelo pré-preenchido na página **'Rever +' para futura** reutilização.

Também pode ativar a funcionalidade [hdInsight ID Broker](identity-broker.md) durante a criação do cluster. A funcionalidade ID Broker permite-lhe iniciar sedutação em Ambari utilizando a Autenticação Multi-Factor e obter os bilhetes Kerberos necessários sem precisar de hashes de senha em Azure AD DS.

> [!NOTE]  
> Os primeiros seis caracteres dos nomes do cluster ESP devem ser únicos no seu ambiente. Por exemplo, se tiver vários clusters ESP em diferentes redes virtuais, escolha uma convenção de nomeação que garanta que os primeiros seis caracteres nos nomes do cluster são únicos.

![Validação de domínio para pacote de segurança empresarial Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Depois de ativar o ESP, as configurações erradas comuns relacionadas com o Azure AD DS são automaticamente detetadas e validadas. Depois de corrigir estes erros, pode continuar com o próximo passo.

![Azure HDInsight Enterprise Security Package falhou na validação de domínio](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Quando criar um cluster HDInsight com ESP, deve fornecer os seguintes parâmetros:

* **Utilizador de administração de cluster**: Escolha um administrador para o seu cluster a partir da sua instância AD DS sincronizada. Esta conta de domínio já deve estar sincronizada e disponível em Azure AD DS.

* **Grupos de acesso ao cluster**: Os grupos de segurança cujos utilizadores pretende sincronizar e ter acesso ao cluster devem estar disponíveis no Azure AD DS. Um exemplo é o grupo HiveUsers. Para obter mais informações, consulte [Criar um grupo e adicionar membros no Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **URL LDAPS**: Um exemplo é `ldaps://contoso.com:636` .

A identidade gerida que criou pode ser escolhida a partir da lista de abandono de **identidade gerida atribuída pelo Utilizador** quando está a criar um novo cluster.

![Azure HDInsight ESP Ative Directory Domain Services geriu identidade](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Passos seguintes

* Para configurar as políticas de Colmeia e executar consultas de Colmeia, consulte [as políticas de colmeia Apache configuradas para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para utilizar o SSH para ligar aos clusters HDInsight com ESP, consulte [Use SSH com Apache Hadoop baseado em Linux em HDInsight de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
