---
title: Garantir aplicações PaaS utilizando | de armazenamento Azure Microsoft Docs
description: Saiba mais sobre as melhores práticas de segurança do Azure Storage para garantir as suas aplicações web e móveis PaaS.
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
ms.openlocfilehash: 12cce0eac3827046ae9171f3dd5696ae5905c802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093977"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Melhores práticas para garantir aplicações web e móveis PaaS usando O Azure Storage
Neste artigo, discutimos uma coleção de boas práticas de segurança do Azure Storage para garantir as suas aplicações web e móveis de plataforma como serviço (PaaS). Estas boas práticas derivam da nossa experiência com o Azure e das experiências de clientes como você.

O Azure permite implantar e utilizar o armazenamento de formas que não são facilmente alcançáveis no local. Com o armazenamento Azure, você pode alcançar altos níveis de escalabilidade e disponibilidade com relativamente pouco esforço. Não só o Azure Storage é a base para máquinas virtuais Windows e Linux Azure, como também pode suportar grandes aplicações distribuídas.

O Azure Storage fornece os seguintes quatro serviços: armazenamento de bolhas, armazenamento de mesa, armazenamento de fila e armazenamento de ficheiros. Para saber mais, consulte [Introdução ao Microsoft Azure Storage](../../storage/common/storage-introduction.md).

O [guia de segurança do Azure Storage](../../storage/blobs/security-recommendations.md) é uma excelente fonte para informações detalhadas sobre o armazenamento e segurança da Azure. Este artigo de boas práticas aborda a um nível elevado alguns dos conceitos encontrados no guia de segurança e links para o guia de segurança, bem como outras fontes, para mais informações.

Este artigo aborda as seguintes boas práticas:

- Assinaturas de acesso partilhado (SAS)
- Controlo de acesso baseado em funções do Azure (RBAC do Azure)
- Encriptação do lado do cliente para dados de alto valor
- Encriptação do Serviço de Armazenamento


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Use uma assinatura de acesso partilhado em vez de uma chave de conta de armazenamento
O controlo de acesso é crítico. Para ajudá-lo a controlar o acesso ao Azure Storage, o Azure gera duas chaves de conta de armazenamento de 512 bits (SAKs) quando cria uma conta de armazenamento. O nível de redundância chave permite evitar interrupções de serviço durante a rotação da chave de rotina. 

As chaves de acesso ao armazenamento são segredos prioritários e só devem ser acessíveis aos responsáveis pelo controlo do acesso ao armazenamento. Se as pessoas erradas tiverem acesso a estas teclas, terão o controlo total do armazenamento e poderão substituir, eliminar ou adicionar ficheiros ao armazenamento. Isto inclui malware e outros tipos de conteúdo que podem potencialmente comprometer a sua organização ou os seus clientes.

Você ainda precisa de uma maneira de fornecer acesso a objetos no armazenamento. Para fornecer mais acesso granular pode aproveitar a assinatura de acesso partilhado (SAS). O SAS permite-lhe partilhar objetos específicos no armazenamento para um intervalo de tempo pré-definido e com permissões específicas. Uma assinatura de acesso partilhado permite-lhe definir:

- O intervalo sobre o qual o SAS é válido, incluindo a hora de início e o prazo de validade.
- As permissões concedidas pelo SAS. Por exemplo, um SAS numa bolha pode conceder a um utilizador ler e escrever permissões para essa bolha, mas não eliminar permissões.
- Um endereço IP opcional ou um leque de endereços IP a partir dos quais o Azure Storage aceita o SAS. Por exemplo, pode especificar uma série de endereços IP pertencentes à sua organização. Isto fornece outra medida de segurança para o seu SAS.
- O protocolo sobre o qual a Azure Storage aceita o SAS. Pode utilizar este parâmetro opcional para restringir o acesso aos clientes através do HTTPS.

O SAS permite-lhe partilhar o conteúdo da forma como pretende partilhá-lo sem dar as chaves da sua conta de armazenamento. Utilizar sempre o SAS na sua aplicação é uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves da sua conta de armazenamento.

Para saber mais sobre a assinatura de acesso partilhado, consulte [Usando assinaturas de acesso partilhado.](../../storage/common/storage-sas-overview.md) 

## <a name="use-azure-role-based-access-control"></a>Use o controlo de acesso baseado em funções Azure
Outra forma de gerir o acesso é utilizar o [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md). Com o Azure RBAC, concentra-se em dar aos colaboradores as permissões exatas de que necessitam, com base na necessidade de conhecer e menos privilégios de princípios de segurança. Demasiadas permissões podem expor uma conta aos agressores. Poucas permissões significam que os empregados não conseguem fazer o seu trabalho de forma eficiente. O Azure RBAC ajuda a resolver este problema oferecendo uma gestão de acesso fino para a Azure. Isto é imperativo para as organizações que querem impor políticas de segurança para o acesso aos dados.

Você pode usar funções incorporadas Azure em Azure para atribuir privilégios aos utilizadores. Por exemplo, utilize o Storage Account Contributor para os operadores de nuvem que precisam de gerir as contas de armazenamento e a função de contribuinte clássico da conta de armazenamento para gerir contas clássicas de armazenamento. Para os operadores de nuvem que precisam de gerir VMs mas não a rede virtual ou conta de armazenamento a que estão conectados, pode adicioná-los à função De Contribuinte de Máquina Virtual.

As organizações que não impõem o controlo do acesso aos dados utilizando capacidades como o Azure RBAC podem estar a dar mais privilégios do que o necessário para os seus utilizadores. Isto pode levar a um compromisso de dados, permitindo a alguns utilizadores o acesso a dados que não deveriam ter em primeiro lugar.

Para saber mais sobre a Azure RBAC consulte:

- [Atribuir funções Azure usando o portal Azure](../../role-based-access-control/role-assignments-portal.md)
- [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md)
- [Guia de segurança de Armazenamento do Azure](../../storage/blobs/security-recommendations.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Use encriptação do lado do cliente para dados de alto valor
A encriptação do lado do cliente permite-lhe encriptar programáticamente dados em trânsito antes de fazer o upload para o Azure Storage e desencriptar programáticamente os dados ao recuperá-lo. Isto fornece encriptação de dados em trânsito, mas também fornece encriptação de dados em repouso. A encriptação do lado do cliente é o método mais seguro para encriptar os seus dados, mas requer que faça alterações programáticas na sua aplicação e coloque processos de gestão chave no lugar.

A encriptação do lado do cliente também lhe permite ter o controlo exclusivo sobre as suas chaves de encriptação. Pode gerar e gerir as suas próprias chaves de encriptação. Utiliza uma técnica de envelope onde a biblioteca do cliente de armazenamento Azure gera uma chave de encriptação de conteúdo (CEK) que é então embrulhada (encriptada) usando a chave de encriptação chave (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétrica ou uma chave simétrica e pode ser gerido localmente ou armazenado em [Azure Key Vault](../../key-vault/general/overview.md).

A encriptação do lado do cliente é incorporada nas bibliotecas de clientes de armazenamento .NET. Consulte [a encriptação do lado do cliente e o Cofre de Chaves Azure para o Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md) para obter informações sobre encriptar dados dentro das aplicações do cliente e gerar e gerir as suas próprias chaves de encriptação.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Ativar encriptação do serviço de armazenamento para obter dados em repouso
Quando [a encriptação do serviço de armazenamento](../../storage/common/storage-service-encryption.md) para armazenamento de ficheiros é ativada, os dados são encriptados automaticamente utilizando encriptação AES-256. A Microsoft lida com toda a encriptação, desencriptação e gestão de chaves. Esta funcionalidade está disponível para tipos de despedimentos LRS e GRS.

## <a name="next-steps"></a>Passos seguintes

Este artigo apresentou-lhe uma coleção de boas práticas de segurança do Azure Storage para garantir as suas aplicações web e móveis PaaS. Para saber mais sobre a segurança das suas implementações paaS, consulte:

- [Proteger implementações PaaS](paas-deployments.md)
- [Garantir aplicações web e móveis paaS usando serviços de aplicações Azure](paas-applications-using-app-services.md)
- [Proteger bases de dados de PaaS no Azure](paas-applications-using-sql.md)