---
title: Segurança Empresarial com Azure AD DS - Azure HDInsight
description: Aprenda a configurar e configurar um pacote de pacotes de segurança empresarial HDInsight utilizando os Serviços de Domínio de Diretório Ativo Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18,seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 2b4756990162817087b0904a764b97526c3545d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186656"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Configurações do Pacote de Segurança Empresarial com Serviços de Domínio de Diretório Ativo Azure em HDInsight

Os clusters do Pacote de Segurança Empresarial (ESP) fornecem acesso multiutilizador em clusters Azure HDInsight. Os clusters HDInsight com ESP estão ligados a um domínio. Esta ligação permite que os utilizadores de domínio utilizem as suas credenciais de domínio para autenticar com os clusters e executar grandes trabalhos de dados.

Neste artigo, aprende-se a configurar um cluster HDInsight com ESP utilizando os Serviços de Domínio de Diretório Ativo Azure (Azure AD DS).

> [!NOTE]  
> O ESP está geralmente disponível em HDInsight 3.6 e 4.0 para estes tipos de cluster: Apache Spark, Interactive, Hadoop e HBase. EsP para o tipo de cluster Apache Kafka está em pré-visualização apenas com o melhor apoio de esforço. Os clusters esp criados antes da data esp GA (1 de outubro de 2018) não são suportados.

## <a name="enable-azure-ad-ds"></a>Ativar o Azure AD DS

> [!NOTE]  
> Apenas os administradores de inquilinos têm os privilégios de permitir a Azure AD DS. Se o armazenamento do cluster for Azure Data Lake Storage Gen1 ou Gen2, deve desativar a autenticação multi-factor Azure apenas para os utilizadores que precisarem de aceder ao cluster utilizando a autenticação básica de Kerberos.
>
> Só é possível utilizar [IPs fidedignos](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou [acesso condicional](../../active-directory/conditional-access/overview.md) para desativar a autenticação multi-factor para utilizadores específicos *apenas* quando estiverem a aceder à gama IP para a rede virtual do cluster HDInsight. Se estiver a utilizar o Acesso Condicional, certifique-se de que o ponto final do serviço Ative Directory está ativado na rede virtual HDInsight.
>
> Se o armazenamento do cluster for armazenamento Azure Blob, não desative a autenticação multi-factor.

Ativar o AD DS Azure é um pré-requisito antes de poder criar um cluster HDInsight com ESP. Para mais informações, consulte [enable Azure Ative Directory Domain Services utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance.md).

Quando o Azure AD DS está ativado, todos os utilizadores e objetos começam a sincronizar do Azure Ative Directory (Azure AD) para o Azure AD DS por padrão. O comprimento da operação de sincronização depende do número de objetos em Azure AD. A sincronização pode levar alguns dias para centenas de milhares de objetos.

O nome de domínio que usa com O DS Azure deve ter 39 caracteres ou menos, para trabalhar com o HDInsight.

Pode optar por sincronizar apenas os grupos que precisam de acesso aos clusters HDInsight. Esta opção de sincronizar apenas determinados grupos chama-se *sincronização por âmbito.* Para obter instruções, consulte a [sincronização de configuração de 'AD' do Azure para o seu domínio gerido](../../active-directory-domain-services/scoped-synchronization.md).

Quando estiver a permitir um LDAP seguro, coloque o nome de domínio no nome do assunto. E o nome alternativo do sujeito no certificado. Se o seu nome de domínio for *contoso100.onmicrosoft.com,* certifique-se de que o nome exato existe no nome do seu certificado e no nome alternativo do sujeito. Para obter mais informações, consulte [Configure secure LDAP para um domínio gerido por DS Azure AD](../../active-directory-domain-services/tutorial-configure-ldaps.md).

O exemplo seguinte cria um certificado auto-assinado. O nome *de* domínio `Subject` contoso100.onmicrosoft.com está `DnsName` em ambos (nome de assunto) e (nome alternativo do sujeito).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Verifique o estado de saúde da AD DS azure

Veja o estado de saúde dos Serviços de Domínio de Diretório Ativo Azure selecionando **a Saúde** na categoria **Gerir.** Certifique-se de que o estado do Azure AD DS é verde (em execução) e a sincronização está completa.

![Saúde Azure AD DS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Criar e autorizar uma identidade gerida

Utilize uma *identidade gerida atribuída* ao utilizador para simplificar as operações de serviços de domínio seguros. Ao atribuir a função de Colaborador de Serviços de **Domínio HDInsight** à identidade gerida, pode ler, criar, modificar e eliminar operações de serviços de domínio.

Certas operações de serviços de domínio, tais como a criação de OUs e os principais de serviços, são necessárias para o Pacote de Segurança Empresarial HDInsight. Pode criar identidades geridas em qualquer subscrição. Para obter mais informações sobre identidades geridas em geral, consulte [identidades geridas para os recursos Do Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Para obter mais informações sobre como as identidades geridas funcionam no Azure HDInsight, consulte [identidades geridas no Azure HDInsight](../hdinsight-managed-identities.md).

Para configurar clusters ESP, crie uma identidade gerida atribuída ao utilizador se ainda não tiver uma. Vê. [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

Em seguida, atribuir o papel de Colaborador de Serviços de **Domínio HDInsight** à identidade gerida no controlo de **acesso** para Azure AD DS. Precisa de privilégios de administração da AD DS azure para fazer esta atribuição de papel.

![Controlo de acesso ao domínio do diretório ativo Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Atribuir a função de Colaborador de Serviços de`on behalf of` **Domínio HDInsight** garante que esta identidade tem acesso adequado ( ) para fazer operações de serviços de domínio no domínio Azure AD DS. Estas operações incluem a criação e a eliminar As Us.

Após a identidade gerida ser dada o papel, o administrador da AD DS Azure gere quem a utiliza. Primeiro, o administrador seleciona a identidade gerida no portal. Em seguida, seleciona o Controlo de **Acesso (IAM)** sob **visão geral**. O administrador atribui a função de **Operador de Identidade Gerida** a utilizadores ou grupos que pretendam criar clusters ESP.

Por exemplo, o administrador azure AD DS pode atribuir esta função ao grupo **MarketingTeam** para a identidade gerida pelo **SJMSI.** Um exemplo é mostrado na imagem seguinte. Esta atribuição garante que as pessoas certas na organização podem usar a identidade gerida para criar clusters ESP.

![Atribuição de função de operador de identidade gerido hDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Considerações de rede

> [!NOTE]  
> O Azure AD DS deve ser implantado numa rede virtual baseada em Recursos Azure. As redes virtuais clássicas não são suportadas para o Azure AD DS. Para mais informações, consulte [enable Azure Ative Directory Domain Services utilizando o portal Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Ativar o Azure AD DS. Em seguida, um servidor local do Sistema de Nome de Domínio (DNS) funciona nas máquinas virtuais do Diretório Ativo (VMs). Configure a sua rede virtual Azure AD DS para utilizar estes servidores DNS personalizados. Para localizar os endereços IP certos, selecione **Propriedades** na categoria **Gerir** e procure em **IP ADDRESS NA REDE VIRTUAL**.

![Localizar endereços IP para servidores DNS locais](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Altere a configuração dos servidores DNS na rede virtual Azure AD DS. Para utilizar estes IPs personalizados, selecione **servidores DNS** na categoria **Definições.** Em seguida, selecione a opção **Custom,** introduza o primeiro endereço IP na caixa de texto e selecione **Guardar**. Adicione mais endereços IP utilizando os mesmos passos.

![Atualizar a configuração dNS da rede virtual](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

É mais fácil colocar tanto a instância Azure AD DS como o cluster HDInsight na mesma rede virtual Azure. Se planeia utilizar diferentes redes virtuais, deve analisar essas redes virtuais para que o controlador de domínio seja visível para VMs HDInsight. Para mais informações, consulte o peering da [rede Virtual.](../../virtual-network/virtual-network-peering-overview.md)

Depois de as redes virtuais serem espartadas, configure a rede virtual HDInsight para utilizar um servidor DNS personalizado. E introduza os IPs privados da AD DS azure como o servidor DNS endereços. Quando ambas as redes virtuais utilizam os mesmos servidores DNS, o seu nome de domínio personalizado irá resolver-se no IP certo e será acessível a partir do HDInsight. Por exemplo, se o `contoso.com`seu nome de `ping contoso.com` domínio for , então após este passo, deve ser resolvido para o IP AD DS Azure direito.

![Configurar servidores DNS personalizados para uma rede virtual peered](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se estiver a utilizar as regras do grupo de segurança de rede (NSG) na sua subnet HDInsight, deverá permitir os [IPs necessários](../hdinsight-management-ip-addresses.md) tanto para o tráfego de entrada como para a saída.

Para testar a configuração da sua rede, junte-se a um VM do Windows na rede/subnet virtual HDInsight e pingo o nome de domínio. (Deve ser resolvido a um IP.) Executar **ldp.exe** para aceder ao domínio Azure AD DS. Em seguida, junte-se a este VM do Windows para o domínio para confirmar que todas as chamadas RPC necessárias têm sucesso entre o cliente e o servidor.

Utilize o **nslookup** para confirmar o acesso à rede à sua conta de armazenamento. Ou qualquer base de dados externa que possa utilizar (por exemplo, metaloja externa da Hive ou Ranger DB). Certifique-se de que as [portas necessárias](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) são permitidas nas regras NSG da sub-rede Azure AD DS, se um NSG garantir O DS Azure. Se a junção de domínio deste Windows VM for bem sucedida, então pode continuar até ao próximo passo e criar clusters ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Criar um cluster HDInsight com ESP

Depois de configurar corretamente os passos anteriores, o próximo passo é criar o cluster HDInsight com ESP ativado. Ao criar um cluster HDInsight, pode ativar o Pacote de Segurança empresarial no separador **de rede Security +.** Para um modelo de Gestor de Recursos Azure para implantação, use a experiência do portal uma vez. Em seguida, baixe o modelo pré-preenchido no **Review + crie** página para futura reutilização.

Também pode ativar a funcionalidade [HDInsight ID Broker](identity-broker.md) durante a criação do cluster. A funcionalidade ID Broker permite-lhe iniciar sessão na Ambari utilizando a Autenticação Multi-Factor e obter os bilhetes Kerberos necessários sem precisar de hashes de senha em Azure AD DS.

> [!NOTE]  
> Os primeiros seis caracteres dos nomes do cluster ESP devem ser únicos no seu ambiente. Por exemplo, se tiver vários clusters ESP em diferentes redes virtuais, escolha uma convenção de nomeação que garanta que os primeiros seis caracteres nos nomes do cluster são únicos.

![Validação de domínio para Pacote de Segurança Empresarial Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Depois de ativar o ESP, as configurações erradas comuns relacionadas com o DS Azure são automaticamente detetadas e validadas. Depois de corrigir estes erros, pode continuar com o próximo passo.

![Pacote de segurança empresarial Azure HDInsight falhou na validação do domínio](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Quando criar um cluster HDInsight com ESP, deve fornecer os seguintes parâmetros:

* **Utilizador administrativo**do cluster : Escolha um administrador para o seu cluster a partir da sua instância AD DS Azure sincronizada. Esta conta de domínio já deve estar sincronizada e disponível em DS AD Azure.

* **Grupos**de acesso ao cluster : Os grupos de segurança cujos utilizadores deseja sincronizar e ter acesso ao cluster devem estar disponíveis no Azure AD DS. Um exemplo é o grupo HiveUsers. Para mais informações, consulte [Criar um grupo e adicionar membros no Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **URL LDAPS**: `ldaps://contoso.com:636`Um exemplo é .

A identidade gerida que criou pode ser escolhida a partir da lista de abandono de **identidade gerida pelo Utilizador** quando estiver a criar um novo cluster.

![Azure HDInsight ESP Ative Directory Domain Services gerido identidade](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Passos seguintes

* Para configurar as políticas da Colmeia e executar consultas de Hive, consulte as políticas de [Configure Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para utilizar o SSH para se ligar aos clusters HDInsight com ESP, consulte [Use SSH com Apache Hadoop baseado em Linux no HDInsight a partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
