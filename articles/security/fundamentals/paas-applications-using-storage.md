---
title: Protegendo aplicativos PaaS usando o armazenamento do Azure | Microsoft Docs
description: Saiba mais sobre as práticas recomendadas de segurança de armazenamento do Azure para proteger seus aplicativos móveis e Web de PaaS.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 675e10101d01d831aad7652c70cbfcf320085a3c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999174"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Práticas recomendadas para proteger aplicativos Web e móveis de PaaS usando o armazenamento do Azure
Neste artigo, discutiremos uma coleção de práticas recomendadas de segurança de armazenamento do Azure para proteger seus aplicativos móveis e Web de PaaS (plataforma como serviço). Essas práticas recomendadas derivam de nossa experiência com o Azure e as experiências de clientes como você.

O Azure possibilita implantar e usar o armazenamento de maneiras que não podem ser facilmente alcançados localmente. Com o armazenamento do Azure, você pode alcançar altos níveis de escalabilidade e disponibilidade com relativamente pouco esforço. Não só o armazenamento do Azure é a base para máquinas virtuais do Azure para Windows e Linux, ele também pode dar suporte a aplicativos distribuídos grandes.

O armazenamento do Azure fornece os quatro serviços a seguir: Armazenamento de BLOBs, armazenamento de tabelas, armazenamento de filas e armazenamento de arquivos. Para saber mais, confira [introdução ao armazenamento do Microsoft Azure](/azure/storage/common/storage-introduction).

O [Guia de segurança do armazenamento do Azure](/azure/storage/common/storage-security-guide) é uma excelente fonte para obter informações detalhadas sobre o armazenamento e a segurança do Azure. Este artigo de práticas recomendadas aborda em um alto nível alguns dos conceitos encontrados no guia de segurança e links para o guia de segurança, bem como outras fontes, para obter mais informações.

Este artigo aborda as seguintes práticas recomendadas:

- Assinaturas de acesso partilhado (SAS)
- Controlo de acesso baseado em funções (RBAC)
- Criptografia do lado do cliente para dados de alto valor
- Encriptação do Serviço de Armazenamento


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Usar uma assinatura de acesso compartilhado em vez de uma chave de conta de armazenamento
O controle de acesso é crítico. Para ajudá-lo a controlar o acesso ao armazenamento do Azure, o Azure gera SAKs (chaves de conta de armazenamento) de 2 512 bits quando você cria uma conta de armazenamento. O nível de redundância de chave torna possível evitar interrupções de serviço durante a rotação de chaves de rotina. 

As chaves de acesso de armazenamento são segredos de prioridade alta e só devem ser acessíveis aos responsáveis pelo controle de acesso de armazenamento. Se as pessoas erradas obtiverem acesso a essas chaves, elas terão controle total do armazenamento e poderão substituir, excluir ou adicionar arquivos ao armazenamento. Isso inclui malware e outros tipos de conteúdo que podem potencialmente comprometer sua organização ou seus clientes.

Você ainda precisa de uma maneira de fornecer acesso a objetos no armazenamento. Para fornecer acesso mais granular, você pode aproveitar a SAS (assinatura de acesso compartilhado). A SAS possibilita que você compartilhe objetos específicos no armazenamento por um intervalo de tempo predefinido e com permissões específicas. Uma assinatura de acesso compartilhado permite que você defina:

- O intervalo em que a SAS é válida, incluindo a hora de início e a hora de expiração.
- As permissões concedidas pela SAS. Por exemplo, uma SAS em um blob pode conceder a um usuário permissões de leitura e gravação para esse BLOB, mas não permissões de exclusão.
- Um endereço IP opcional ou um intervalo de endereços IP do qual o armazenamento do Azure aceita a SAS. Por exemplo, você pode especificar um intervalo de endereços IP pertencentes à sua organização. Isso fornece outra medida de segurança para a sua SAS.
- O protocolo no qual o armazenamento do Azure aceita a SAS. Você pode usar esse parâmetro opcional para restringir o acesso a clientes usando HTTPS.

A SAS permite que você compartilhe conteúdo da maneira que deseja compartilhá-lo sem descartar suas chaves de conta de armazenamento. Sempre usar SAS em seu aplicativo é uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer suas chaves de conta de armazenamento.

Para saber mais sobre a assinatura de acesso compartilhado, consulte [usando assinaturas de acesso compartilhado](/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 

## <a name="use-role-based-access-control"></a>Utilizar o controlo de acesso baseado em funções
Outra maneira de gerenciar o acesso é usar o RBAC ( [controle de acesso baseado em função](/azure/role-based-access-control/overview) ). Com o RBAC, você se concentra em fornecer aos funcionários as permissões exatas de que precisam, com base na necessidade de conhecer os princípios de segurança de privilégios mínimos. Muitas permissões podem expor uma conta a invasores. Permissões insuficientes significam que os funcionários não podem realizar seu trabalho com eficiência. O RBAC ajuda a resolver esse problema oferecendo gerenciamento de acesso refinado para o Azure. Isso é imperativo para organizações que desejam impor políticas de segurança para acesso a dados.

Você pode usar funções RBAC internas no Azure para atribuir privilégios a usuários. Por exemplo, use o colaborador da conta de armazenamento para operadores de nuvem que precisam gerenciar contas de armazenamento e a função de colaborador da conta de armazenamento clássica para gerenciar contas de armazenamento clássicas. Para operadores de nuvem que precisam gerenciar VMs, mas não a rede virtual ou a conta de armazenamento à qual eles estão conectados, você pode adicioná-los à função colaborador da máquina virtual.

As organizações que não impõem o controle de acesso a dados usando recursos como o RBAC podem estar concedendo mais privilégios do que o necessário para seus usuários. Isso pode levar ao comprometimento de dados, permitindo que alguns usuários acessem dados que não deveriam ter em primeiro lugar.

Para saber mais sobre o RBAC, consulte:

- [Gerir o acesso através do RBAC e do portal do Azure](/azure/role-based-access-control/role-assignments-portal)
- [Built-in roles for Azure resources](/azure/role-based-access-control/built-in-roles) (Funções incorporadas para recursos do Azure)
- [Guia de segurança do Armazenamento do Azure](/azure/storage/common/storage-security-guide) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Usar criptografia do lado do cliente para dados de alto valor
A criptografia do lado do cliente permite que você criptografe programaticamente os dados em trânsito antes de carregar no armazenamento do Azure e descriptografe os dados de forma programática ao recuperá-los. Isso fornece criptografia de dados em trânsito, mas também fornece criptografia de dados em repouso. A criptografia do lado do cliente é o método mais seguro de criptografar seus dados, mas exige que você faça alterações programáticas em seu aplicativo e coloque os processos de gerenciamento de chaves em vigor.

A criptografia do lado do cliente também permite que você tenha controle exclusivo sobre suas chaves de criptografia. Você pode gerar e gerenciar suas próprias chaves de criptografia. Ele usa uma técnica de envelope em que a biblioteca de cliente de armazenamento do Azure gera uma CEK (chave de criptografia de conteúdo) que é então encapsulada (criptografada) usando a chave de criptografia de chave (KEK). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciado localmente ou armazenado em [Azure Key Vault](/azure/key-vault/key-vault-overview).

A criptografia do lado do cliente é incorporada às bibliotecas de cliente de armazenamento do Java e do .NET. Consulte [criptografia do lado do cliente e Azure Key Vault para armazenamento do Microsoft Azure](/azure/storage/common/storage-client-side-encryption) para obter informações sobre como criptografar dados em aplicativos cliente e gerar e gerenciar suas próprias chaves de criptografia.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Habilitar Criptografia do Serviço de Armazenamento para dados em repouso
Quando [criptografia do serviço de armazenamento](/azure/storage/common/storage-service-encryption) para armazenamento de arquivos é habilitado, os dados são criptografados automaticamente usando a criptografia AES-256. A Microsoft lida com toda a criptografia, descriptografia e gerenciamento de chaves. Esse recurso está disponível para tipos de redundância LRS e GRS.

## <a name="next-steps"></a>Passos Seguintes

Este artigo apresentou a você uma coleção de práticas recomendadas de segurança de armazenamento do Azure para proteger seus aplicativos móveis e Web de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteger implementações PaaS](paas-deployments.md)
- [Protegendo aplicativos Web e móveis de PaaS usando serviços Azure Apps](paas-applications-using-app-services.md)
- [Protegendo bancos de dados PaaS no Azure](paas-applications-using-sql.md)
