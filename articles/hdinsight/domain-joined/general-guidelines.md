---
title: Diretrizes gerais de segurança empresarial em Azure HDInsight
description: Algumas boas práticas que devem facilitar a implantação e gestão do Pacote de Segurança Empresarial.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463209"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Informações e diretrizes gerais de segurança empresarial no Azure HDInsight

Ao implantar um cluster HDInsight seguro, existem algumas boas práticas que devem facilitar a implementação e gestão do cluster. Algumas informações gerais e orientações são discutidas aqui.

## <a name="use-of-secure-cluster"></a>Utilização de cluster seguro

### <a name="recommended"></a>Recomendado

* O cluster será utilizado por vários utilizadores ao mesmo tempo.
* Os utilizadores têm diferentes níveis de acesso aos mesmos dados.

### <a name="not-necessary"></a>Não é necessário

* Você vai executar apenas trabalhos automatizados (como conta de utilizador único), um cluster padrão é bom o suficiente.
* Pode fazer a importação de dados utilizando um cluster padrão e utilizar a mesma conta de armazenamento num cluster seguro diferente, onde os utilizadores podem executar trabalhos de análise.

## <a name="use-of-local-account"></a>Utilização da conta local

* Se utilizar uma conta de utilizador partilhada ou uma conta local, então será difícil identificar quem usou a conta para alterar o config ou o serviço.
* A utilização de contas locais é problemática quando os utilizadores já não fazem parte da organização.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Políticas

* Por padrão, ranger usa **Deny** como a política.

* Quando o acesso aos dados é feito através de um serviço em que a autorização está ativada:
  * O plugin de autorização ranger é invocado e dado o contexto do pedido.
  * Ranger aplica as políticas configuradas para o serviço. Se as políticas dos Rangers falharem, a verificação de acesso é adiada para o sistema de ficheiros. Alguns serviços como o MapReduce apenas verificam se o ficheiro/pasta que está a ser propriedade do mesmo utilizador que está a enviar o pedido. Serviços como a Hive, verifique se quer`rwx`a correspondência de propriedade quer as permissões adequadas do sistema de ficheiros ().

* Para a Hive, além de ter as permissões para fazer `rwx`Criar / Atualizar / Eliminar permissões, o utilizador deve ter permissões no diretório no armazenamento e em todos os subdiretórios.

* As políticas podem ser aplicadas a grupos (preferíveis) em vez de indivíduos.

* O autorizador ranger avaliará todas as políticas ranger para esse serviço para cada pedido. Esta avaliação pode ter um impacto no tempo que leva para aceitar o trabalho ou a consulta.

### <a name="storage-access"></a>Acesso ao armazenamento

* Se o tipo de armazenamento for WASB, então não está envolvido nenhum símbolo oAuth.
* Se o Ranger tiver realizado a autorização, o acesso ao armazenamento acontece utilizando a Identidade Gerida.
* Se o Ranger não efetuou qualquer autorização, então o acesso ao armazenamento acontece usando o símbolo OAuth do utilizador.

### <a name="hierarchical-name-space"></a>Espaço hierárquico

Quando o espaço hierárquico do nome não está ativado:

* Não há permissões herdadas.
* Apenas a permissão do sistema de ficheiros que funciona é a função **De Armazenamento Data XXXX** RBAC, a ser atribuída ao utilizador diretamente no portal Azure.

### <a name="default-hdfs-permissions"></a>Permissões HDFS padrão

* Por padrão, os utilizadores não **/** têm acesso à pasta no HDFS (precisam de estar na função do proprietário do blob de armazenamento para ter acesso ao sucesso).
* Para o diretório de encenação para mapreduce e outros, `sticky _wx` é criado um diretório específico do utilizador e fornecido permissões. Os utilizadores podem criar ficheiros e pastas por baixo, mas não podem olhar para outros itens.

### <a name="url-auth"></a>Url auth

Se o auth url estiver ativado:

* O config config conterá os prefixos que `adl://`estão cobertos no auth url (como).
* Se o acesso for para este url, então o Ranger verificará se o utilizador está na lista de espera.
* Ranger não vai verificar nenhuma das políticas de grãos finos.

## <a name="resource-groups"></a>Grupos de recursos

Utilize um novo grupo de recursos para cada cluster para que possa distinguir entre recursos de cluster.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSGs, firewalls e gateway interno

* Utilize grupos de segurança de rede (NSGs) para bloquear redes virtuais.
* Use firewall para lidar com as políticas de acesso de saída.
* Use o portal interno que não está aberto à internet pública.

## <a name="azure-active-directory"></a>Azure Active Directory

[O Azure Ative Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft.

### <a name="policies"></a>Políticas

* Desative a política de acesso condicional utilizando a política baseada em endereçoip. Isto requer que os pontos finais do serviço sejam ativados nos VNETs onde os clusters são implantados. Se utilizar um serviço externo para MFA (algo que não seja AAD), a política baseada em endereçoIP não funcionará

* `AllowCloudPasswordValidation`a política é necessária para os utilizadores federados. Uma vez que o HDInsight utiliza o nome de utilizador/palavra-passe diretamente para obter fichas da Azure AD, esta política tem de ser ativada para todos os utilizadores federados.

* Ative pontos finais de serviço se necessitar de um bypass de acesso condicional utilizando IPs fidedignos.

### <a name="groups"></a>Grupos

* Sempre implante agrupamentos com um grupo.
* Utilize a AD Azure para gerir membros do grupo (mais fácil do que tentar gerir os serviços individuais no cluster).

### <a name="user-accounts"></a>Contas de utilizador

* Utilize uma conta única de utilizador para cada cenário. Por exemplo, utilize uma conta para importação, utilize outra para consulta ou outros postos de trabalho de transformação.
* Use políticas ranger baseadas em grupo em vez de políticas individuais.
* Tenha um plano sobre como gerir utilizadores que não deveriam ter mais acesso a clusters.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[O Azure Ative Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) fornece serviços de domínio geridos, tais como a adesão ao domínio, a política de grupo, o protocolo de acesso ao diretório leve (LDAP) e a autenticação Kerberos /NTLM que é totalmente compatível com o Diretório Ativo do Windows Server.

O Azure AD DS é necessário para que clusters seguros se juntem a um domínio.
O HDInsight não pode depender de controladores de domínio no local ou controladores de domínio personalizados, uma vez que introduz demasiados pontos de falha, partilha de credenciais, permissões DNS, e assim por diante. Para mais informações, consulte [as FAQs Azure DS DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Instância Azure AD DS

* Crie a `.onmicrosoft.com domain`instância com o . Desta forma, não haverá vários servidores DNS a servir o domínio.
* Crie um certificado auto-assinado para o LDAPS e carregue-o para o Azure AD DS.
* Utilize uma rede virtual peered para implementar clusters (quando tiver várias equipas a implementar clusters ESP HDInsight, isso será útil). Isto garante que não precisa de abrir portas (NSGs) na rede virtual com controlador de domínio.
* Configure corretamente o DNS para a rede virtual (o nome de domínio Azure AD DS deve ser resolvido sem quaisquer entradas de ficheiros dos anfitriões).
* Se estiver a restringir o tráfego de saída, certifique-se de que leu através do suporte de [firewall no HDInsight](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Propriedades sincronizadas de Azure AD a Azure AD DS

* A Azure AD liga sincronizações do local ao Azure AD.
* Azure AD DS sincroniza da Azure AD.

Azure AD DS sincroniza periodicamente objetos de Azure AD. A lâmina Azure AD DS no portal Azure apresenta o estado de sincronização. Durante cada fase de sincronização, propriedades únicas podem entrar em conflito e rebatizadas. Preste atenção ao mapeamento da propriedade de Azure AD para Azure AD DS.

Para mais informações, consulte a população de Utilizadores [AD Azure](../../active-directory/hybrid/plan-connect-userprincipalname.md), e como funciona a sincronização do [Azure AD DS](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Sincronização de hash de palavra-passe

* As palavras-passe são sincronizadas de forma diferente dos outros tipos de objetos. Apenas hashes de senha não reversíveis são sincronizados em Azure AD e Azure AD DS
* No local para Azure AD tem de ser ativado através do AD Connect
* A sincronização Azure AD DS é automática (as latenciências são menores de 20 minutos).
* As hashes de senha só são sincronizadas quando há uma palavra-passe alterada. Quando ativa o sync de hash de palavra-passe, todas as palavras-passe existentes não são sincronizadas automaticamente, uma vez que são armazenadas irreversivelmente. Quando mudaa a palavra-passe, as hashes de senha são sincronizadas.

### <a name="computer-objects-location"></a>Localização de objetos de computador

Cada cluster está associado a um único Ou. Um utilizador interno é aprovisionado na U. Todos os nós são domínio saqueado no mesmo Ou.

### <a name="active-directory-administrative-tools"></a>Ferramentas administrativas de Diretório Ativo

Para obter passos sobre como instalar as ferramentas administrativas do Diretório Ativo num VM do Servidor Windows, consulte [Instalar ferramentas](../../active-directory-domain-services/tutorial-create-management-vm.md)de gestão .

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="cluster-creation-fails-repeatedly"></a>A criação de cluster falha repetidamente

Razões mais comuns:

* A configuração dNS não está correta, a união de domínios dos nós do cluster falha.
* Os NSGs são demasiado restritivos, impedindo a adesão do domínio.
* Identidade gerida não tem permissões suficientes.
* O nome do cluster não é exclusivo nos primeiros seis caracteres (seja com outro cluster ao vivo, ou com um cluster apagado).

## <a name="next-steps"></a>Passos seguintes

* [Configurações do Pacote de Segurança Empresarial com Serviços de Domínio de Diretório Ativo Azure em HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Synchronize utilizadores de Diretório Ativo Azure para um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md).
