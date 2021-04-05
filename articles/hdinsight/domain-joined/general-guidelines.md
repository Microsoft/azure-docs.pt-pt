---
title: Diretrizes gerais de segurança da empresa em Azure HDInsight
description: Algumas boas práticas que devem facilitar a implementação e gestão do Pacote de Segurança Empresarial.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933562"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Informações e orientações gerais de segurança da empresa em Azure HDInsight

Ao implementar um cluster HDInsight seguro, existem algumas boas práticas que devem facilitar a implementação e gestão do cluster. Algumas informações gerais e orientações são discutidas aqui.

## <a name="use-of-secure-cluster"></a>Utilização de clusters seguros

### <a name="recommended"></a>Recomendado

* O cluster será utilizado por vários utilizadores ao mesmo tempo.
* Os utilizadores têm diferentes níveis de acesso aos mesmos dados.

### <a name="not-necessary"></a>Não é necessário

* Você vai executar apenas trabalhos automatizados (como uma única conta de utilizador), um cluster padrão é bom o suficiente.
* Você pode fazer a importação de dados usando um cluster padrão e usar a mesma conta de armazenamento em um cluster diferente seguro onde os utilizadores podem executar trabalhos de análise.

## <a name="use-of-local-account"></a>Utilização de conta local

* Se utilizar uma conta de utilizador partilhada ou uma conta local, então será difícil identificar quem usou a conta para alterar o config ou o serviço.
* A utilização de contas locais é problemática quando os utilizadores já não fazem parte da organização.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Políticas

* Por defeito, ranger usa **Deny** como a apólice.

* Quando o acesso aos dados é escorrido através de um serviço em que a autorização está ativada:
  * O plugin de autorização ranger é invocado e dado o contexto do pedido.
  * Ranger aplica as políticas configuradas para o serviço. Se as políticas dos Rangers falharem, a verificação de acesso será adiada para o sistema de ficheiros. Alguns serviços como o MapReduce só verificam se o ficheiro/pasta é propriedade do mesmo utilizador que está a enviar o pedido. Serviços como a Hive, verifique se a propriedade corresponde ou permissões apropriadas do sistema de ficheiros `rwx` ().

* Para a Hive, além de ter as permissões para fazer permissões de Criar/ Atualizar/ Apagar, o utilizador deve ter `rwx` permissões no diretório no armazenamento e em todos os sub-directórios.

* As políticas podem ser aplicadas a grupos (preferíveis) em vez de indivíduos.

* O autor do Ranger avaliará todas as políticas dos Rangers para esse serviço para cada pedido. Esta avaliação pode ter um impacto no tempo que demora a aceitar o trabalho ou consulta.

### <a name="storage-access"></a>Acesso ao armazenamento

* Se o tipo de armazenamento for WASB, então não está envolvido nenhum token OAuth.
* Se o Ranger tiver efetuado a autorização, então o acesso ao armazenamento acontece utilizando a Identidade Gerida.
* Se o Ranger não efetuou qualquer autorização, então o acesso ao armazenamento acontece usando o token OAuth do utilizador.

### <a name="hierarchical-name-space"></a>Espaço de nome hierárquico

Quando o espaço hierárquico do nome não está ativado:

* Não há permissões herdadas.
* Apenas a permissão do sistema de ficheiros que funciona é a função **de Armazenamento de Dados XXXX** Azure, a atribuir diretamente ao utilizador no portal Azure.

### <a name="default-hdfs-permissions"></a>Permissões HDFS predefinidos

* Por padrão, os utilizadores não têm acesso à **/** pasta em HDFS (precisam de estar na função de proprietário do blob de armazenamento para terem acesso a ter sucesso).
* Para o diretório de encenação para mapreduce e outros, é criado um diretório específico do utilizador e `sticky _wx` permissões fornecidas. Os utilizadores podem criar ficheiros e pastas por baixo, mas não podem olhar para outros itens.

### <a name="url-auth"></a>Auth URL

Se o url auth estiver ativado:

* O config conterá quais os prefixos cobertos no url auth `adl://` (como).
* Se o acesso for para este url, então o Ranger verificará se o utilizador está na lista de autorizações.
* Ranger não vai verificar nenhuma das políticas de grãos finos.

## <a name="resource-groups"></a>Grupos de recursos

Utilize um novo grupo de recursos para cada cluster para que possa distinguir entre recursos de cluster.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSGs, firewalls e gateway interno

* Utilize grupos de segurança de rede (NSGs) para bloquear redes virtuais.
* Utilize firewall para lidar com as políticas de acesso de saída.
* Use o portal interno que não está aberto à internet pública.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Ative Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft.

### <a name="policies"></a>Políticas

* Desative a política de acesso condicional utilizando a política baseada no endereço IP. Isto requer que os pontos finais de serviço sejam ativados nos VNETs onde os clusters são implantados. Se utilizar um serviço externo para MFA (algo diferente do AAD), a política baseada no endereço IP não funcionará

* `AllowCloudPasswordValidation` é necessária uma política para os utilizadores federados. Uma vez que o HDInsight utiliza o nome de utilizador/palavra-passe diretamente para obter fichas da Azure AD, esta política tem de ser ativada para todos os utilizadores federados.

* Ativar os pontos finais do serviço se necessitar de um bypass de acesso condicional utilizando IPs fidedignos.

### <a name="groups"></a>Grupos

* Coloque sempre os aglomerados com um grupo.
* Utilize a Azure AD para gerir os membros do grupo (mais fácil do que tentar gerir os serviços individuais no cluster).

### <a name="user-accounts"></a>Contas de utilizador

* Utilize uma conta de utilizador única para cada cenário. Por exemplo, utilize uma conta para importação, utilize outra para consulta ou outros trabalhos de transformação.
* Use políticas ranger baseadas em grupo em vez de políticas individuais.
* Tenha um plano para gerir utilizadores que não deveriam ter mais acesso a clusters.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[A azure Ative Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) fornece serviços de domínio geridos, tais como a adesão de domínio, a política de grupo, o protocolo de acesso ao diretório leve (LDAP) e a autenticação Kerberos /NTLM que é totalmente compatível com o Windows Server Ative Directory.

O Azure AD DS é necessário para que os clusters seguros se juntem a um domínio.
O HDInsight não pode depender de controladores de domínio ou controladores de domínio personalizados, uma vez que introduz demasiados pontos de falha, partilha de credenciais, permissões de DNS, e assim por diante. Para mais informações, consulte [as FAQs AD AD AZure.](../../active-directory-domain-services/faqs.md)

### <a name="azure-ad-ds-instance"></a>Exemplo de Azure AD DS

* Criar o caso com o `.onmicrosoft.com domain` . Desta forma, não haverá vários servidores DNS a servir o domínio.
* Crie um certificado auto-assinado para o LDAPS e faça o upload para Azure AD DS.
* Utilize uma rede virtual espreitada para implantar clusters (quando tiver várias equipas a implantar clusters HDInsight ESP, isso será útil). Isto garante que não precisa de abrir portas (NSGs) na rede virtual com controlador de domínio.
* Configure corretamente o DNS para a rede virtual (o nome de domínio Azure AD DS deve resolver sem entradas de ficheiros de anfitriões).
* Se estiver a restringir o tráfego de saída, certifique-se de que leu através do suporte de [firewall em HDInsight](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Propriedades sincronizadas de Azure AD a Azure AD DS

* Azure AD conecta sincronizações do local ao Azure AD.
* Azure AD DS sincroniza da Azure AD.

Azure AD DS sincroniza periodicamente os objetos do Azure AD. A lâmina Azure AD DS no portal Azure apresenta o estado de sincronização. Durante cada fase de sincronização, propriedades únicas podem entrar em conflito e renomeadas. Preste atenção ao mapeamento da propriedade de Azure AD a Azure AD DS.

Para obter mais informações, consulte [a população do Nome do Utilizador Azure AD](../../active-directory/hybrid/plan-connect-userprincipalname.md), e [como funciona a sincronização da Azure AD DS](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Sincronização de haxixe de palavra-passe

* As palavras-passe são sincronizadas de forma diferente de outros tipos de objetos. Apenas hashes de palavra-passe não reversíveis são sincronizados em Azure AD e Azure AD DS
* No local para a Azure AD tem de ser ativado através do AD Connect
* A azure AD a Azure AD DS sync é automático (as latências são menores de 20 minutos).
* Os hashes de palavra-passe só são sincronizados quando há uma palavra-passe alterada. Quando ativa a sincronização de haxixe de palavra-passe, todas as palavras-passe existentes não são sincronizadas automaticamente, uma vez que são armazenadas de forma irreversível. Quando se altera a palavra-passe, as hashes de palavra-passe ficam sincronizadas.

### <a name="computer-objects-location"></a>Localização de objetos de computador

Cada cluster está associado a um único U. Um utilizador interno é a provisionado na U. Todos os nós são domínio unidos na mesma U.

### <a name="active-directory-administrative-tools"></a>Ferramentas administrativas do Diretório Ativo

Para obter etapas sobre como instalar as ferramentas administrativas do Ative Directory num VM do Servidor do Windows, consulte [as ferramentas de gestão de instalação](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="cluster-creation-fails-repeatedly"></a>A criação de cluster falha repetidamente

Razões mais comuns:

* A configuração do DNS não está correta, a união de domínios dos nós de cluster falha.
* Os NSGs são demasiado restritivos, impedindo a união do domínio.
* A Identidade Gerida não tem permissões suficientes.
* O nome do cluster não é único nos primeiros seis caracteres (seja com outro cluster ao vivo, ou com um cluster apagado).

## <a name="authentication-setup-and-configuration"></a>Configuração e configuração de autenticação

### <a name="user-principal-name-upn"></a>Nome principal do utilizador (UPN)

* Por favor, use minúsculas para todos os serviços - UPNs não são sensíveis a casos em clusters ESP, mas
* O prefixo UPN deve coincidir com o nome SAMAccountName em Azure AD-DS. Não é necessário combinar com o campo de correio.

### <a name="ldap-properties-in-ambari-configuration"></a>Propriedades LDAP na configuração Ambari

Para obter uma lista completa das propriedades Ambari que afetam a sua configuração do cluster HDInsight, consulte [a Configuração de Autenticação Ambari LDAP](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html).

## <a name="next-steps"></a>Passos seguintes

* [Configurações de pacotes de segurança da empresa com serviços de domínio de diretório ativo Azure em HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Sincronizar os utilizadores do Azure Ative Directory para um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md).
