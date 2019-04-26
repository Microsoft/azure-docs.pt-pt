---
title: Proteger aplicações PaaS através do armazenamento do Azure | Documentos da Microsoft
description: Saiba mais sobre segurança de armazenamento do Azure melhores práticas para proteger as suas aplicações móveis e web de PaaS.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: TomShinder
ms.openlocfilehash: 3ad97c7adb5901c1da1d174d12d5d6a91831cc74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445422"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Melhores práticas para proteger o PaaS web e móveis com o armazenamento do Azure
Neste artigo, discutimos a uma coleção de segurança de armazenamento do Azure melhores práticas para proteger a sua plataforma-como-serviço (PaaS) aplicações web e móveis. Essas práticas recomendadas são derivadas da nossa experiência com o Azure e as experiências dos clientes, como.

O Azure torna possível implementar e utilizar o armazenamento de formas não facilmente realizáveis no local. Com o armazenamento do Azure, pode entrar altos níveis de escalabilidade e disponibilidade com relativamente pouco esforço. Não só é a base de armazenamento do Azure para Windows e máquinas virtuais do Linux do Azure, também pode suportar grandes aplicativos distribuídos.

Armazenamento do Azure fornece os seguintes quatro serviços: Armazenamento de BLOBs, armazenamento de tabelas, armazenamento de filas e armazenamento de ficheiros. Para obter mais informações, consulte [introdução ao armazenamento do Microsoft Azure](../storage/storage-introduction.md).

O [guia de segurança do armazenamento do Azure](../storage/common/storage-security-guide.md) é uma ótima fonte para obter informações detalhadas sobre o armazenamento do Azure e segurança. Este artigo de melhores práticas de endereços num alto nível alguns dos conceitos encontrados na guia de segurança e ligações para o guia de segurança, bem como outras origens, para obter mais informações.

Este artigo aborda as melhores práticas seguintes:

- Assinaturas de acesso partilhado (SAS)
- Controlo de acesso baseado em funções (RBAC)
- Encriptação do lado do cliente para os dados de valor elevado
- Encriptação do Serviço de Armazenamento


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Utilizar uma assinatura de acesso partilhado, em vez de uma chave de conta de armazenamento
Controlo de acesso é essencial. Para o ajudar a controlar o acesso ao armazenamento do Azure, Azure gera duas chaves de conta de armazenamento de 512 bits (SAKs) quando criar uma conta de armazenamento. O nível de redundância chave torna possível para evitar interrupções do serviço durante a rotação de chaves de rotina. 

Chaves de acesso de armazenamento são segredos de alta prioridade e só devem estar acessíveis para os responsáveis pela controlo de acesso de armazenamento. Se as pessoas erradas obtém acesso a essas chaves, eles serão têm controlo total de armazenamento e poderia substituir, eliminar ou adicionar ficheiros ao armazenamento. Isto inclui o software maligno e outros tipos de conteúdo que potencialmente podem comprometer a sua organização ou os seus clientes.

Ainda precisa de uma forma para fornecer acesso a objetos no armazenamento. Para fornecer acesso mais granular pode tirar partido de assinatura de acesso partilhado (SAS). A SAS torna possível para que possa partilhar objetos específicos no armazenamento durante um intervalo de tempo predefinido e com permissões específicas. Uma assinatura de acesso partilhado permite-lhe definir:

- O intervalo durante o qual a SAS é válida, incluindo a hora de início e a hora de expiração.
- As permissões concedidas a SAs. Por exemplo, um SAS num blob poderá conceder uma leitura de utilizador e permissões de escrita a esse blob, mas não eliminar as permissões.
- Um endereço IP opcional ou intervalo de endereços IP do qual o armazenamento do Azure aceita a SAS. Por exemplo, poderá especificar um intervalo de endereços IP que pertençam a sua organização. Isto proporciona outra medida de segurança para a SAS.
- O protocolo através do qual o armazenamento do Azure aceita a SAS. Pode utilizar este parâmetro opcional para restringir o acesso aos clientes através de HTTPS.

SAS permite-lhe partilhar conteúdo a forma como pretende partilhá-lo sem dar as chaves de conta de armazenamento. Sempre através da SAS em seu aplicativo é uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves de conta de armazenamento.

Para saber mais sobre a assinatura de acesso partilhado, veja [assinaturas de acesso partilhado do Using](../storage/common/storage-dotnet-shared-access-signature-part-1.md). 

## <a name="use-role-based-access-control"></a>Utilizar o controlo de acesso baseado em funções
Outra forma de gerir o acesso é usar [controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC). Com o RBAC, pode concentrar-se em dar aos funcionários as exatas permissões necessárias, com base na necessidade de conhecer e princípios de segurança de privilégio mínimo. Demasiadas permissões podem expor uma conta para os atacantes. Permissões insuficientes significa que os funcionários não é possível obter a trabalhar com eficiência. RBAC Ajuda a resolver esse problema, oferecendo a gestão de acessos detalhada para o Azure. Isso é fundamental para as organizações que desejam aplicar políticas de segurança para acesso a dados.

Pode utilizar funções RBAC incorporadas no Azure para atribuir privilégios a utilizadores. Por exemplo, utilize contribuinte de conta de armazenamento para os operadores de cloud que precisam de gerir contas de armazenamento e a função de contribuinte de conta de armazenamento clássica para gerir contas de armazenamento clássicas. Para os operadores de cloud que precisam de gerir VMs, mas não a rede virtual ou a conta de armazenamento ao qual estão ligados, pode adicioná-los para a função de contribuinte de Máquina Virtual.

As organizações que não é impor o controlo de acesso de dados ao utilizar as capacidades, como o RBAC podem ser a dar mais privilégios do que o necessário para os seus utilizadores. Isto pode levar ao comprometimento de dados ao permitir que alguns usuários acesso aos dados que não devem ter em primeiro lugar.

Para saber mais sobre o RBAC, veja:

- [Gerir o acesso através do RBAC e do portal do Azure](../role-based-access-control/role-assignments-portal.md)
- [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md) (Funções incorporadas para recursos do Azure)
- [Guia de segurança do Armazenamento do Azure](../storage/common/storage-security-guide.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Utilizar a encriptação do lado do cliente para os dados de valor elevado
Encriptação do lado do cliente permite-lhe encriptar programaticamente os dados em trânsito antes de carregar para o armazenamento do Azure e, por meio de programação desencriptar dados quando recuperá-lo. Isso fornece encriptação de dados em trânsito, mas também fornece encriptação de dados em repouso. Encriptação do lado do cliente é o método mais seguro de criptografar seus dados, mas ele exige que faça alterações programáticas ao seu aplicativo e colocar os processos de gestão de chaves no local.

Encriptação do lado do cliente também lhe permite ter controlo exclusivo sobre as suas chaves de encriptação. Pode gerar e gerir as suas próprias chaves de encriptação. Ele usa uma técnica de envelope em que a biblioteca de cliente de armazenamento do Azure gera uma conteúdo chave de encriptação (CEK) que, em seguida, é encapsulada (encriptada) com a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e podem ser gerenciada localmente ou armazenada no [do Azure Key Vault](../key-vault/key-vault-whatis.md).

Encriptação do lado do cliente está incorporada no Java e as bibliotecas de cliente de armazenamento do .NET. Ver [encriptação do lado do cliente e o Azure Key Vault para o armazenamento do Microsoft Azure](../storage/storage-client-side-encryption.md) para obter informações sobre a encriptação de dados dentro de aplicativos de cliente e a gerar e gerir as suas próprias chaves de encriptação.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Ativar a encriptação do serviço de armazenamento para dados Inativos
Quando [Storage Service Encryption](../storage/storage-service-encryption.md) para armazenamento de ficheiros é ativado, os dados são encriptados automaticamente com a encriptação de AES-256. Microsoft lida com todos os a encriptação, desencriptação e gestão de chaves. Esta funcionalidade está disponível para tipos de redundância LRS e GRS.

## <a name="next-steps"></a>Passos Seguintes

Este artigo apresentou-a uma coleção de segurança de armazenamento do Azure melhores práticas para proteger as aplicações móveis e web de PaaS. Para saber mais sobre como proteger as suas implementações de PaaS, consulte:

- [Proteger implementações PaaS](security-paas-deployments.md)
- [Proteger web de PaaS e aplicativos móveis usando os serviços de aplicações do Azure](security-paas-applications-using-app-services.md)
- [Protegendo bancos de dados de PaaS no Azure](security-paas-applications-using-sql.md)
