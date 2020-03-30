---
title: Garantir aplicações PaaS utilizando o Armazenamento Azure [ Microsoft Docs
description: Conheça as melhores práticas de segurança do Armazenamento Azure para garantir as suas aplicações web e móveis PaaS.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70999174"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Boas práticas para garantir aplicações web e móveis paaS usando o Armazenamento Azure
Neste artigo, discutimos uma coleção de boas práticas de segurança de Armazenamento Azure para garantir as suas aplicações web e móveis (PaaS) de plataforma como serviço.paaS. Estas boas práticas derivam da nossa experiência com o Azure e das experiências de clientes como você.

O Azure permite a implantação e utilização do armazenamento de formas não facilmente alcançáveis no local. Com o armazenamento Azure, você pode alcançar altos níveis de escalabilidade e disponibilidade com relativamente pouco esforço. Não só o Azure Storage é a base para windows e Máquinas Virtuais Linux Azure, como também pode suportar grandes aplicações distribuídas.

O Azure Storage fornece os seguintes quatro serviços: armazenamento de blob, armazenamento de mesa, armazenamento de fila e armazenamento de ficheiros. Para saber mais, consulte [Introdução ao Armazenamento Microsoft Azure](/azure/storage/common/storage-introduction).

O guia de [segurança Azure Storage](/azure/storage/common/storage-security-guide) é uma excelente fonte para informações detalhadas sobre o Armazenamento e segurança do Azure. Este artigo de boas práticas aborda a um alto nível alguns dos conceitos encontrados no guia de segurança e ligações ao guia de segurança, bem como outras fontes, para mais informações.

Este artigo aborda as seguintes boas práticas:

- Assinaturas de acesso partilhado (SAS)
- Controlo de acesso baseado em funções (RBAC)
- Encriptação do lado do cliente para dados de alto valor
- Encriptação do Serviço de Armazenamento


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Use uma assinatura de acesso partilhado em vez de uma chave de conta de armazenamento
O controlo de acesso é fundamental. Para ajudá-lo a controlar o acesso ao Armazenamento Azure, o Azure gera duas chaves de conta de armazenamento de 512 bits (SAKs) quando cria uma conta de armazenamento. O nível de redundância chave permite evitar interrupções de serviço durante a rotação da tecla de rotina. 

As chaves de acesso ao armazenamento são segredos de alta prioridade e só devem ser acessíveis aos responsáveis pelo controlo de acesso ao armazenamento. Se as pessoas erradas tiverem acesso a estas chaves, terão controlo total do armazenamento e poderão substituir, eliminar ou adicionar ficheiros ao armazenamento. Isto inclui malware e outros tipos de conteúdo que podem potencialmente comprometer a sua organização ou os seus clientes.

Ainda precisa de uma forma de fornecer acesso a objetos armazenados. Para proporcionar um acesso mais granular pode tirar partido da assinatura de acesso partilhado (SAS). O SAS permite-lhe partilhar objetos específicos no armazenamento para um intervalo de tempo pré-definido e com permissões específicas. Uma assinatura de acesso partilhado permite-lhe definir:

- O intervalo sobre o qual o SAS é válido, incluindo o tempo de início e o tempo de validade.
- As permissões concedidas pela SAS. Por exemplo, um SAS numa bolha pode conceder a um utilizador ler e escrever permissões a essa bolha, mas não apagar permissões.
- Um endereço IP opcional ou uma gama de endereços IP a partir dos quais o Armazenamento Azure aceita o SAS. Por exemplo, pode especificar uma série de endereços IP pertencentes à sua organização. Isto fornece mais uma medida de segurança para o seu SAS.
- O protocolo sobre o qual o Azure Storage aceita o SAS. Pode utilizar este parâmetro opcional para restringir o acesso aos clientes que usam HTTPS.

A SAS permite-lhe partilhar conteúdos da forma que pretende partilhá-lo sem dar as chaves da sua conta de armazenamento. Usar sempre o SAS na sua aplicação é uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves da sua conta de armazenamento.

Para saber mais sobre a assinatura de acesso partilhado, consulte [A utilização de assinaturas de acesso partilhado.](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 

## <a name="use-role-based-access-control"></a>Utilizar o controlo de acesso baseado em funções
Outra forma de gerir o acesso é utilizar o [controlo de acesso baseado em funções](/azure/role-based-access-control/overview) (RBAC). Com o RBAC, foca-se em dar aos colaboradores as permissões exatas de que necessitam, com base na necessidade de conhecer e menos privilegiar os princípios de segurança. Demasiadas permissões podem expor uma conta aos agressores. Poucas permissões significam que os empregados não conseguem fazer o seu trabalho de forma eficiente. O RBAC ajuda a resolver este problema oferecendo uma gestão de acesso de grãos finos para o Azure. Isto é imperativo para as organizações que querem impor políticas de segurança para o acesso de dados.

Pode utilizar funções RBAC incorporadas em Azure para atribuir privilégios aos utilizadores. Por exemplo, utilize o Contribuinte de Conta de Armazenamento para operadores em nuvem que precisam de gerir contas de armazenamento e papel de Contribuinte de Conta de Armazenamento Clássico para gerir contas de armazenamento clássicas. Para os operadores em nuvem que precisam de gerir VMs mas não a rede virtual ou conta de armazenamento a que estão conectados, pode adicioná-los à função De Contribuinte de Máquina Virtual.

As organizações que não impõem o controlo de acesso de dados utilizando capacidades como o RBAC podem estar a dar mais privilégios do que o necessário para os seus utilizadores. Isto pode levar ao compromisso de dados, permitindo a alguns utilizadores o acesso a dados que não deveriam ter em primeiro lugar.

Para saber mais sobre rBAC ver:

- [Gerir o acesso através do RBAC e do portal do Azure](/azure/role-based-access-control/role-assignments-portal)
- [Built-in roles for Azure resources](/azure/role-based-access-control/built-in-roles) (Funções incorporadas para recursos do Azure)
- [Guia de segurança de armazenamento azure](/azure/storage/common/storage-security-guide) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Use encriptação do lado do cliente para dados de alto valor
A encriptação do lado do cliente permite-lhe encriptar programáticamente os dados em trânsito antes de enviar para o Azure Storage e desencriptar programáticamente os dados ao recuperá-lo. Isto fornece encriptação de dados em trânsito, mas também fornece encriptação de dados em repouso. A encriptação do lado do cliente é o método mais seguro de encriptar os seus dados, mas requer que faça alterações programáticas na sua aplicação e coloque os processos de gestão chave no lugar.

A encriptação do lado do cliente também lhe permite ter o controlo exclusivo sobre as suas chaves de encriptação. Pode gerar e gerir as suas próprias chaves de encriptação. Utiliza uma técnica de envelope onde a biblioteca de clientes de armazenamento Azure gera uma chave de encriptação de conteúdo (CEK) que é então embrulhada (encriptada) usando a chave de encriptação (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétrica ou uma chave simétrica e pode ser gerido localmente ou armazenado em [Azure Key Vault](/azure/key-vault/key-vault-overview).

A encriptação do lado do cliente é incorporada nas bibliotecas de clientes de armazenamento .NET. Consulte a [encriptação do lado do Cliente e o Cofre de Chaves Azure para o Microsoft Azure Storage](/azure/storage/common/storage-client-side-encryption) para obter informações sobre encriptar dados dentro das aplicações do cliente e gerar e gerir as suas próprias chaves de encriptação.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Ativar encriptação do serviço de armazenamento para dados em repouso
Quando a [encriptação do serviço](/azure/storage/common/storage-service-encryption) de armazenamento para armazenamento de ficheiros está ativada, os dados são encriptados automaticamente utilizando encriptação AES-256. A Microsoft lida com toda a encriptação, desencriptação e gestão de chaves. Esta funcionalidade está disponível para tipos de redundância LRS e GRS.

## <a name="next-steps"></a>Passos seguintes

Este artigo apresentou-lhe uma coleção de boas práticas de segurança de Armazenamento Azure para garantir as suas aplicações web e móveis PaaS. Para saber mais sobre como garantir as suas implementações paas, consulte:

- [Proteger implementações PaaS](paas-deployments.md)
- [Garantir aplicações web e móveis paaS utilizando serviços de aplicações azure](paas-applications-using-app-services.md)
- [Proteger bases de dados de PaaS no Azure](paas-applications-using-sql.md)
