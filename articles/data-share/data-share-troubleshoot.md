---
title: Resolver Problemas do Azure Data Share
description: Saiba como resolver problemas com convites e erros quando criar ou receber partilhas de dados no Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964512"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Resolução de problemas comuns na Azure Data Share 

Este artigo explica como resolver problemas comuns no Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Convites Azure Data Share 

Em alguns casos, quando novos utilizadores selecionam **Aceitar Convite** num convite por e-mail, podem ver uma lista vazia de convites. 

:::image type="content" source="media/no-invites.png" alt-text="Screenshot mostrando uma lista vazia de convites.":::

Este problema pode ter uma das seguintes causas:

* **O serviço Azure Data Share não está registado como fornecedor de recursos de qualquer subscrição da Azure no inquilino do Azure.** Este problema acontece quando o seu inquilino Azure não tem recurso de Partilha de Dados. 

    Quando cria um recurso do Azure Data Share, regista automaticamente o fornecedor de recursos na sua subscrição do Azure. Pode registar manualmente o serviço Partilha de Dados utilizando os seguintes passos. Para completar estes passos, precisa do [papel de Contribuinte](../role-based-access-control/built-in-roles.md#contributor) para a subscrição do Azure. 

    1. No portal do Azure, aceda a **Subscrições**.
    1. Selecione a subscrição que pretende utilizar para criar o recurso Azure Data Share.
    1. Selecione **Fornecedores de Recursos**.
    1. Pesquisa rumo **ao Microsoft.DataShare**.
    1. Selecione **Registar**.

* **O convite é enviado para o seu pseudónimo de e-mail em vez do seu endereço de e-mail de inscrição no Azure.** Se já registou o serviço Azure Data Share ou criou um recurso de Partilha de Dados no inquilino do Azure, mas ainda não consegue ver o convite, o seu pseudónimo de e-mail pode estar listado como destinatário. Contacte o seu fornecedor de dados e certifique-se de que o convite será enviado para o seu endereço de e-mail de inscrição do Azure e não para o seu pseudónimo de e-mail.

* **O convite já foi aceite.** O link no e-mail leva-o à página **data share Invitations** no portal Azure. Esta página lista apenas convites pendentes. Convites aceites não aparecem na página. Para visualizar as ações recebidas e configurar a definição do cluster target Azure Data Explorer, vá ao recurso Data Share que usou para aceitar o convite.

## <a name="creating-and-receiving-shares"></a>Criação e receção de ações

Podem surgir os seguintes erros quando criar uma nova partilha, adicionar conjuntos de dados ou conjuntos de dados de mapas:

* Falhou em adicionar conjuntos de dados.
* Falhou em mapear conjuntos de dados.
* Não é possível conceder o acesso x do recurso Data Share ao y.
* Você não tem permissões apropriadas para x.
* Não foi possível adicionar permissões de escrita para a conta Azure Data Share a um ou mais dos seus recursos selecionados.

Pode ver um destes erros se não tiver permissões suficientes para a loja de dados Azure. Para obter mais informações, veja [Funções e requisitos](concepts-roles-permissions.md). 

Precisa da permissão de escrita para partilhar ou receber dados de uma loja de dados Azure. Esta permissão é normalmente parte do papel do Contribuinte. 

Se estiver a partilhar dados ou a receber dados da loja de dados Azure pela primeira vez, também precisa da permissão *microsoft.Autorização/atribuições/função/escrever.* Esta permissão é normalmente parte da função Proprietário. Mesmo que tenha criado o recurso Azure data store, não é necessariamente o proprietário do recurso. 

Quando tiver as permissões adequadas, o serviço Azure Data Share permite automaticamente que a identidade gerida do recurso de partilha de dados aceda à loja de dados. Este processo pode demorar alguns minutos. Se sentir falhas por causa deste atraso, tente novamente após alguns minutos.

A partilha baseada em SQL requer permissões extra. Para obter informações sobre pré-requisitos, consulte [partilhar de fontes SQL](how-to-share-from-sql.md).

## <a name="snapshots"></a>Instantâneos
Um instantâneo pode falhar por várias razões. Abra uma mensagem de erro detalhada selecionando a hora de início do instantâneo e, em seguida, o estado de cada conjunto de dados. 

Os instantâneos geralmente falham por estas razões:

* A Data Share carece de permissão para ler a partir da loja de dados de origem ou para escrever na loja de dados-alvo. Para obter mais informações, veja [Funções e requisitos](concepts-roles-permissions.md). Se estiver a tirar uma fotografia pela primeira vez, o recurso Data Share poderá necessitar de alguns minutos para ter acesso à loja de dados Azure. Depois de alguns minutos, tente de novo.
* A ligação Data Share à loja de dados de origem ou à loja de dados-alvo é bloqueada por uma firewall.
* Foi eliminado um conjunto de dados partilhado, uma loja de dados de origem ou uma loja de dados-alvo.

Para contas de armazenamento, um instantâneo pode falhar porque um ficheiro está a ser atualizado na fonte enquanto o instantâneo está a acontecer. Como resultado, um ficheiro de 0 bytes pode aparecer no alvo. Após a atualização na fonte, as imagens devem ter sucesso.

Para fontes SQL, um instantâneo pode falhar por estas outras razões:

* O script SQL de origem ou o script SQL alvo que concede a permissão de Partilha de Dados não foi executado. Ou para a Azure SQL Database ou Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse), o script executa utilizando a autenticação SQL em vez da autenticação do Azure Ative Directory.  
* A loja de dados de origem ou a loja de dados SQL alvo é pausada.
* O processo instantâneo ou a loja de dados-alvo não suporta tipos de dados SQL. Para mais informações, consulte [partilhar a partir de fontes SQL](how-to-share-from-sql.md#supported-data-types).
* A loja de dados de origem ou a loja de dados SQL alvo é bloqueada por outros processos. A Azure Data Share não bloqueia estas lojas de dados. Mas as fechaduras existentes nestas lojas de dados podem fazer com que uma imagem falhe.
* A tabela SQL alvo é referenciada por uma restrição de chave estrangeira. Durante uma snapshot, se uma tabela alvo tiver o mesmo nome que uma tabela nos dados de origem, a Azure Data Share deixa cair a tabela e cria uma nova tabela. Se a tabela SQL alvo for referenciada por uma restrição de chave estrangeira, a tabela não pode ser largada.
* Um ficheiro CSV alvo é gerado, mas os dados não podem ser lidos no Excel. Pode ver este problema quando a tabela SQL de origem contém dados que incluem caracteres não ingleses. No Excel, selecione o **separador Obter Dados** e escolha o ficheiro CSV. Selecione a origem do ficheiro **65001: Unicode (UTF-8)** e, em seguida, carregue os dados.

## <a name="updated-snapshot-schedules"></a>Horários de instantâneo atualizados
Após o fornecedor de dados atualizar o calendário instantâneo da partilha enviada, o consumidor de dados precisa de desativar o calendário de instantâneos anterior. Em seguida, ative o calendário de instantâneos atualizado para a partilha recebida. 

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue para o tutorial [de dados partilhar.](share-your-data.md) 

Para aprender a receber dados, continue a aceitar e receber o tutorial [de dados.](subscribe-to-data-share.md)