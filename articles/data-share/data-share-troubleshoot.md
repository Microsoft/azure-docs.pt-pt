---
title: Resolver Problemas do Azure Data Share
description: Saiba como resolver problemas com convites e erros ao criar ou receber partilhas de dados com a Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.openlocfilehash: 1b61b643ea4b195878a1d12fc1ac4bb7fef23027
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151365"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Resolver problemas comuns no Azure Data Share 

Este artigo mostra como resolver problemas comuns para a Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Convites Azure Data Share 

Em alguns casos, quando um novo utilizador clica em **Aceitar Convite** a partir do convite por e-mail que foi enviado, eles podem ser apresentados com uma lista vazia de convites. 

![Sem convites](media/no-invites.png)

Este erro pode ocorrer devido aos seguintes motivos:

* **O serviço do Azure Data Share não está registado como um fornecedor de recursos de qualquer subscrição no inquilino do Azure.** Passará por este problema se não houver nenhum recurso do Data Share no inquilino do Azure. Quando cria um recurso do Azure Data Share, regista automaticamente o fornecedor de recursos na sua subscrição do Azure. Também pode registar manualmente o serviço Data Share ao seguir estes passos. Precisará de ter a função Contribuidor do Azure para concluir estes passos.

    1. No portal do Azure, navegue até **Subscrições**
    1. Selecione a subscrição que quer utilizar para criar o recurso do Azure Data Share
    1. Clique em **Fornecedores de Recursos**
    1. Pesquise **Microsoft.DataShare**
    1. Clique em **Registar** 

    Você precisará ter o [papel de Azure Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) para a subscrição do Azure para completar estes passos. 

* **O convite é enviado para o alias de e-mail em vez do seu e-mail de início de sessão do Azure.** Se registou o serviço do Azure Data Share ou já criou um recurso do Data Share no inquilino do Azure, mas ainda não consegue ver o convite, talvez seja porque o fornecedor inseriu o seu alias de e-mail como destinatário, em vez do seu endereço de e-mail de início de sessão do Azure. Entre em contacto com o seu fornecedor de dados e verifique se enviou o convite para o seu endereço de e-mail de início de sessão do Azure e não o seu alias de e-mail.

* **O convite já foi aceite.** A ligação no e-mail encaminha-o para a página Convite do Azure Data Share no portal do Azure, que lista apenas os convites pendentes. Se já tiver aceitado o convite, este deixará de aparecer na página Convite do Azure Data Share. Prossiga para o recurso do Data Share que utilizou para aceitar o convite para ver as partilhas recebidas e configure a sua definição do cluster do Azure Data Explorer de destino.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Erro ao criar ou receber uma nova parte

"Falhou em adicionar conjuntos de dados"

"Falhou em mapear conjuntos de dados"

"Incapaz de conceder recursos de partilha de dados x acesso a y"

"Você não tem permissões apropriadas para x"

"Não conseguimos adicionar permissões de escrita para a conta Azure Data Share a um ou mais dos seus recursos selecionados"

Se receber algum dos erros acima referidos ao criar uma nova partilha ou mapeamento de conjuntos de dados, pode ser devido a permissões insuficientes para a loja de dados Azure. Consulte [as funções e os requisitos](concepts-roles-permissions.md) para as permissões necessárias. 

Precisa de escrever permissão para partilhar ou receber dados de uma loja de dados Azure, que normalmente existe na função Contribuinte. 

Se esta for a primeira vez que partilha ou recebe dados da loja de dados Azure, também precisa da *Microsoft.Authorization/role assignments/write* permission, que normalmente existe na função Proprietário. Mesmo que tenha criado o recurso Azure data store, não o torna automaticamente o proprietário do recurso. Com a devida permissão, o serviço Azure Data Share concede automaticamente o acesso de identidade gerido do recurso à loja de dados. Este processo pode levar alguns minutos a fazer efeito. Se sentir falhas devido a este atraso, tente novamente em alguns minutos.

A partilha baseada em SQL requer permissões adicionais. Consulte [a partilha de fontes SQL](how-to-share-from-sql.md) para obter uma lista detalhada de pré-requisitos.

## <a name="snapshot-failed"></a>O instantâneo falhou
O instantâneo pode falhar devido a uma variedade de razões. Pode encontrar uma mensagem de erro detalhada clicando na hora de início do instantâneo e, em seguida, no estado de cada conjunto de dados. Seguem-se razões comuns para a falha do instantâneo:

* A Data Share não tem permissão para ler a partir da loja de dados de origem ou escrever para a loja de dados-alvo. Consulte [as funções e os requisitos](concepts-roles-permissions.md) para requisitos de permissão detalhados. Se esta for a primeira vez que está a tirar uma fotografia, pode levar alguns minutos para que o recurso Data Share tenha acesso à loja de dados Azure. Espere alguns minutos e tente de novo.
* Data Partilhar a ligação à loja de dados de origem ou alvo é bloqueada por firewall.
* O conjunto de dados partilhado, ou a loja de dados de origem ou alvo é eliminada.

Para as fontes SQL, as seguintes são causas adicionais de falhas de instantâneo. 

* O script SQL de origem ou alvo para conceder a permissão de Partilha de Dados não é executado, ou é executado usando a autenticação SQL em vez da autenticação do Azure Ative Directory.  
* A loja de dados SQL de origem ou alvo é pausada.
* Os tipos de dados SQL não são suportados pelo processo instantâneo ou pela loja de dados-alvo. Consulte [a partilhar a partir de fontes SQL](how-to-share-from-sql.md#supported-data-types) para obter mais detalhes.
* A loja de dados SQL de origem ou alvo é bloqueada por outros processos. A Azure Data Share não aplica fechaduras à loja de dados de origem e alvo SQL. No entanto, as fechaduras existentes na loja de dados SQL de origem e alvo causarão falhas instantâneas.
* A tabela SQL alvo é referenciada por uma restrição de chave estrangeira. Durante o instantâneo, se existir uma tabela-alvo com o mesmo nome, o Azure Data Share deixa cair a tabela e cria uma nova tabela. Se a tabela SQL-alvo for referenciada por uma restrição de chave estrangeira, a tabela não pode ser largada.
* O ficheiro CSV alvo é gerado, mas os dados não podem ser lidos no Excel. Isto pode acontecer quando a tabela SQL de origem contém dados com caracteres não ingleses. No Excel, selecione o separador 'Obter Dados' e escolha o ficheiro CSV, selecione a origem do ficheiro como 65001: Unicode (UTF-8) e carregue os dados.

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md) 

Para aprender a receber dados, continue a aceitar e receber o tutorial [de dados.](subscribe-to-data-share.md)

