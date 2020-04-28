---
title: Troubleshoot Azure NetApp Ficheiros Fornecedor de Recursos Erros de Fornecedor de Recursos / Microsoft Docs
description: Descreve causas, soluções e soluções para erros comuns do Fornecedor de Recursos de Ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: 62e67d4965444df0e731b4387808ed3b89e4673a
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72597198"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Resolver problemas de erros do Fornecedor de Recursos do Azure NetApp Files 

Este artigo descreve erros comuns do Fornecedor de Recursos do Azure NetApp, as suas causas, soluções e soluções (se disponíveis).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Erros do Fornecedor de Recursos de Ficheiros De Recursos do Azure NetApp Comuns

***O BareMetalTenantId não pode ser alterado.***  

Este erro ocorre quando tenta atualizar ou corrigir `BaremetalTenantId` um volume e a propriedade tem um valor alterado.

* Causa:   
Está a tentar atualizar um `BaremetalTenantId` volume e a propriedade tem um valor diferente do valor armazenado em Azure.
* Solução:   
Não inclua `BaremetalTenantId` no patch e no pedido de atualização (put). Em alternativa, `BaremetalTenantId` certifique-se de que é o mesmo no pedido.

***O Nível de Serviço não pode ser alterado.***  

Este erro ocorre quando tenta atualizar ou remendar um conjunto de capacidades com um nível de serviço diferente quando o pool de capacidade já tem volumes no mesmo.

* Causa:   
Está a tentar atualizar um nível de serviço de piscina de capacidade quando a piscina contém volumes.
* Solução:   
Elimine todos os volumes do pool de capacidade e, em seguida, altere o nível de serviço.
* Solução:   
Crie outra piscina de capacidade, em seguida, crie os volumes novamente na nova piscina de capacidade.

***PoolId não pode ser alterado***  

Este erro ocorre quando tenta atualizar ou remendar `PoolId` um conjunto de capacidades com uma propriedade alterada.

* Causa:   
Você está tentando atualizar `PoolId` uma propriedade de piscina de capacidade. A `PoolId` propriedade é uma propriedade apenas para leitura e não pode ser alterada.
* Solução:   
Não inclua `PoolId` no patch e no pedido de atualização (put).  Em alternativa, `PoolId` certifique-se de que é o mesmo no pedido.

***CriaçãoToken não pode ser alterada.***

Este erro ocorre quando tenta alterar a`CreationToken`trajetória do ficheiro ( ) após a criação do volume. O caminho`CreationToken`do ficheiro () deve ser definido quando o volume é criado, e não pode ser alterado mais tarde.

* Causa:   
Está a tentar alterar a`CreationToken`trajetória dos ficheiros ( ) depois de criado o volume, que não é uma operação apoiada. 
* Solução:   
Se não for necessário alterar a trajetória do ficheiro, considere retirar o parâmetro do pedido de desminagem da mensagem de erro.
* Solução:   
Se precisar de alterar o`CreationToken`caminho dos ficheiros, pode criar um novo volume com um novo caminho de ficheiros e, em seguida, migrar os dados para o novo volume.

***CriaçãoToken deve ter pelo menos 16 caracteres de comprimento.***

Este erro ocorre quando o`CreationToken`caminho do ficheiro () não cumpre o requisito de comprimento. O comprimento do caminho do ficheiro deve ter pelo menos um carácter de comprimento.

* Causa:   
O caminho dos ficheiros está vazio.  Quando se cria um volume utilizando a API, é necessário um símbolo de criação. Se estiver a utilizar o portal Azure, o caminho do ficheiro é gerado automaticamente.
* Solução:   
Introduza pelo menos um personagem como o caminho do ficheiro (`CreationToken`).

***O nome do domínio não pode ser alterado.***

Este erro ocorre quando tenta alterar o nome de domínio em Diretório Ativo.

* Causa:   
Está a tentar atualizar a propriedade do nome de domínio.
* Solução:    
Nenhum. Não pode mudar o nome de domínio.
* Solução:   
Elimine todos os volumes utilizando a configuração do Diretório Ativo. Em seguida, elimine a configuração do Diretório Ativo e recrie os volumes.

***Duplicar erro de valor para objeto ExportPolicy.Rules[RuleIndex].***

Este erro ocorre quando a política de exportação não é definida com um índice único. Quando definir as políticas de exportação, todas as regras de política de exportação devem ter um índice único entre 1 e 5.

* Causa:   
A política de exportação definida não satisfaz a exigência de regras de política de exportação. Deve ter uma regra de política de exportação no mínimo e cinco regras de política de exportação no máximo.
* Solução:   
Certifique-se de que o índice ainda não está utilizado e que está na faixa de 1 a 5.
* Solução:   
Use um índice diferente para a regra que está a tentar definir.

***Erro {ação} {resourceTypeName}***

Este erro é detetado quando outro manuseamento de erros não conseguiu lidar com o erro enquanto executa uma ação num recurso.   Inclui texto 'Error'. Pode `{action}` ser qualquer`getting`um `creating` `updating`de `deleting`, , , ou ).  O `{resourceTypeName}` é `resourceTypeName` o (por `capacityPool` `volume`exemplo, `netAppAccount`, , , e assim por diante).

* Causa:   
Este erro é uma exceção não tratada onde a causa não é conhecida.
* Solução:   
Contacte o Centro de Apoio azure para informar a razão detalhada nos registos.
* Solução:   
Nenhum.

***O nome do caminho do ficheiro pode conter letras, números e hífenes ("-"""")") apenas.***

Este erro ocorre quando a trajetória do ficheiro contém caracteres não suportados, por exemplo, um período ("."), vírina (""),sublinha ("_"), ou sinal de dólar ("$").

* Causa:   
O caminho do ficheiro contém caracteres não suportados, por exemplo, um período ("."), víreu (""),sublinha ("_"), ou sinal de dólar ("$").
* Solução:   
Remova caracteres que não sejam letras alfabéticas, números ou hífenes ("-") do caminho de ficheiro que introduziu.
* Solução:   
Pode substituir um sublinhado por um hífen ou utilizar a capitalização em vez de espaços para indicar o início de novas palavras.  Por exemplo, utilize "NewVolume" em vez de "novo volume".

***O Sistema de Ficheiros Id não pode ser alterado.***

Este erro ocorre quando tenta `FileSystemId`mudar .  Mudar `FileSystemdId` não é uma operação apoiada. 

* Causa:   
A identificação do sistema de ficheiros é definida quando o volume é criado. `FileSystemId`não pode ser alterado posteriormente.
* Solução:   
Não inclua `FileSystemId` num patch e atualização (put) pedido.  Em alternativa, `FileSystemId` certifique-se de que é o mesmo no pedido.

***ActiveDirectory com id: '{string}' não existe.***

A `{string}` parte é o valor `ActiveDirectoryId` que inseriu na propriedade para a ligação De Diretório Ativo.

* Causa:   
Quando criou uma conta com a configuração do Diretório Ativo, inseriu um valor para `ActiveDirectoryId` isso, que supostamente está vazio.
* Solução:   
Não inclua `ActiveDirectoryId` no pedido de criação (put).

***Versão api inválida.***

A versão API não é submetida ou contém um valor inválido.

* Causa:   
O valor no parâmetro `api-version` de consulta contém um valor inválido.
* Solução:   
Utilize o valor correto da versão API.  O fornecedor de recursos suporta muitas versões API. O valor está no formato yyy-mm-dd.

***Foi recebido um valor inválido por {1}.***

Esta mensagem indica um erro `RuleIndex` `AllowedClients`nos `UnixReadOnly` `UnixReadWrite`campos `Nfsv3`para, , , , e `Nfsv4`.

* Causa:   
O pedido de validação de entrada falhou em `RuleIndex` `AllowedClients`pelo `UnixReadOnly` `UnixReadWrite`menos `Nfsv`um `Nfsv4`dos seguintes campos: , , , 3, e .
* Solução:   
Certifique-se de que fixa todos os parâmetros necessários e não conflituosos na linha de comando. Por exemplo, não é `UnixReadOnly` `UnixReadWrite` possível definir simultaneamente os parâmetros e os parâmetros.
* Solução:   
Veja a solução acima.

***Gama {0} IP {1} para {2} vlan já está em uso***

Este erro ocorre porque os registos internos das gamas IP utilizadas têm um conflito com o endereço IP recém-atribuído.

* Causa:   
O endereço IP atribuído para a criação de volume já está registado.
A razão pode ser uma criação de volume falhada anteriormente.
* Solução:   
Contacte o Centro de Apoio Azure.

***Valor em falta para '{property}'.***

Este erro indica que falta uma propriedade necessária ao pedido. A corda {propriedade} contém o nome da propriedade desaparecida.

* Causa:   
O pedido de validação de entrada falhou em pelo menos uma das propriedades.
* Solução:   
Certifique-se de que define todas as propriedades necessárias e não conflituosas no pedido, especialmente, a propriedade a partir da mensagem de erro.

***Os Alvos do Monte não podem ser alterados.***

Este erro ocorre quando um utilizador está a tentar atualizar ou remendar a propriedade do Volume MountTargets.

* Causa:   
Está a tentar atualizar `MountTargets` a propriedade de volume. A alteração desta propriedade não é suportada.
* Solução:   
Não inclua `MountTargets` num patch e atualização (put) pedido.  Em alternativa, certifique-se de que `MountTargets` é o mesmo no pedido.

***Nome já em uso.***

Este erro indica que o nome do recurso já está em uso.

* Causa:   
Está a tentar criar um recurso com um nome que é usado para um recurso existente.
* Solução:   
Use um nome único ao criar o recurso.

***Caminho de arquivo já em uso.***

Este erro indica que o caminho de ficheiro para o volume já está em uso.

* Causa:   
Está a tentar criar um volume com um caminho de ficheiro que é o mesmo que um volume existente.
* Solução:   
Utilize um caminho de ficheiro único ao criar o volume.

***Nome muito longo.***

Este erro indica que o nome do recurso não cumpre o requisito máximo de comprimento.

* Causa:   
O nome do recurso é muito longo.
* Solução:   
Use um nome mais curto para o recurso.

***Caminho de arquivo muito longo.***

Este erro indica que a trajetória de ficheiro para o volume não satisfaz o requisito máximo de comprimento.

* Causa:   
O caminho do ficheiro de volume é muito longo.
* Solução:   
Use um caminho de arquivo mais curto.

***Nome muito curto.***

Este erro indica que o nome do recurso não cumpre o requisito mínimo de comprimento.

* Causa:   
O nome do recurso é muito curto.
* Solução:   
Use um nome mais longo para o recurso.

***Caminho de arquivo muito curto.***

Este erro indica que o caminho do ficheiro de volume não cumpre o requisito mínimo de comprimento.

* Causa:   
O caminho do ficheiro de volume é muito curto.
* Solução:   
Aumente o comprimento da trajetória do ficheiro de volume.

***Ficheiros Azure NetApp API inacessíveis.***

A API Azure conta com a API de Ficheiros Azure NetApp para gerir volumes. Este erro indica um problema com a ligação API.

* Causa:   
A API subjacente não está a responder, resultando num erro interno. É provável que este erro seja temporário.
* Solução:   
É provável que a questão seja temporária. O pedido deve ter sucesso depois de algum tempo.
* Solução:   
Nenhum. A API subjacente é essencial para a gestão de volumes.

***Nenhum resultado de operação{0}foi encontrado para ' '.***

Este erro indica que um erro interno está a impedir a operação de ser concluída.

* Causa:   
Ocorreu um erro interno e impediu que a operação se completasse.
* Solução:   
É provável que este erro seja temporário. Aguarde alguns minutos e tente novamente. Se o problema persistir, crie um bilhete para ter apoio técnico para investigar o problema.
* Solução:   
Espere alguns minutos e verifique se o problema persiste.

***Não é permitido misturar tipos de protocoloCIFS e NFS***

Este erro ocorre quando está a tentar criar um Volume e existem os tipos de protocoloCIFS (SMB) e NFS nas propriedades do volume.

* Causa:   
Tanto os tipos de protocoloCIFS (SMB) como nFS são utilizados nas propriedades de volume.
* Solução:   
Remova um dos tipos de protocolo.
* Solução:   
Deixe a propriedade tipo protocolo vazia ou nula.

***Número de itens: {value} para objeto: ExportPolicy.Rules[RuleIndex] está fora do intervalo min-max.***

Este erro ocorre quando as regras da política de exportação não satisfazem o requisito mínimo ou máximo de alcance. Se definir a política de exportação, deve ter uma regra de política de exportação no mínimo e cinco regras de política de exportação no máximo.

* Causa:   
A política de exportação que definiu não cumpre o intervalo exigido.
* Solução:   
Certifique-se de que o índice ainda não está utilizado e que está na faixa de 1 a 5.
* Solução:   
Não é obrigatório utilizar a política de exportação nos volumes. Pode omitir completamente a política de exportação se não precisar de utilizar as regras da política de exportação.

***Apenas um diretório ativo permitido***

Este erro ocorre quando se tenta criar uma configuração de Diretório Ativo, e já existe um para a subscrição na região. O erro também pode ocorrer quando se tenta criar mais do que uma configuração de Diretório Ativo.

* Causa:   
Está a tentar criar (não atualizar) um diretório ativo, mas já existe um.
* Solução:   
Se a configuração do Diretório Ativo não estiver a ser utilizada, pode primeiro eliminar a configuração existente e, em seguida, voltar a tentar a operação de criação.
* Solução:   
Nenhum. Só é permitido um Diretório Ativo.

***Operação '{operation}' não suportada.***

Este erro indica que a operação não está disponível para a subscrição ou recurso ativo.

* Causa:   
A operação não está disponível para a subscrição ou recurso.
* Solução:   
Certifique-se de que a operação está inserida corretamente e que está disponível para o recurso e subscrição que está a utilizar.

***OwnerId não pode ser alterado***

Este erro ocorre quando tenta alterar a propriedade OwnerId do volume. Mudar o OwnerId não é uma operação apoiada. 

* Causa:   
A `OwnerId` propriedade é definida quando o volume é criado. A propriedade não pode ser alterada posteriormente.
* Solução:   
Não inclua `OwnerId` num patch e atualização (put) pedido. Em alternativa, certifique-se de que `OwnerId` é o mesmo no pedido.

***Piscina dos pais não encontrada***

Este erro ocorre quando se tenta criar um volume e não se encontra um conjunto de capacidades no qual está a criar o volume.

* Causa:   
A piscina de capacidade onde o volume está a ser criado não é encontrada.
* Solução:   
Muito provavelmente a piscina não foi totalmente criada ou já foi eliminada no momento da criação de volume.

***A operação de patch não é suportada para este tipo de recurso.***

Este erro ocorre quando tenta alterar o alvo de montagem ou o instantâneo.

* Causa:   
O alvo de montagem é definido quando é criado, e não pode ser alterado posteriormente.
As fotos não contêm nenhuma propriedade que possa ser alterada.
* Solução:   
Nenhum. Esses recursos não têm propriedades que possam ser alteradas.

***Tamanho da piscina muito pequeno para o tamanho total do volume.***

Este erro ocorre quando está a atualizar o tamanho da piscina de `usedBytes` capacidade, e o tamanho é menor do que o valor total de todos os volumes nessa piscina de capacidade.  Este erro também pode ocorrer quando está a criar um novo volume ou a redimensionar um volume existente, e o novo tamanho de volume excede o espaço livre na piscina de capacidade.

* Causa:   
Você está tentando atualizar a piscina de capacidade para um tamanho menor do que os bytes usados em todos os volumes na piscina de capacidade.  Ou, você está tentando criar um volume que é maior do que o espaço livre na piscina de capacidade.  Em alternativa, está a tentar redimensionar um volume e o novo tamanho excede o espaço livre na piscina de capacidade.
* Solução:   
Detete o tamanho da piscina de capacidade para um valor maior, ou crie um volume menor para um volume.
* Solução:   
Remova volumes suficientes para que o tamanho da piscina de capacidade possa ser atualizado para este tamanho.

***A propriedade: A localização para Snapshot deve ser a mesma que volume***

Este erro ocorre quando está a criar um instantâneo com outra localização que não o volume que possui o instantâneo.

* Causa:   
Valor inválido na propriedade Localização para o instantâneo.
* Solução:   
Detete uma corda válida na propriedade Local.

***O nome {resourceType} deve ser o mesmo que o nome do identificador de recursos.***

Este erro ocorre quando está a criar um recurso, e preenche a propriedade `resourceId`do nome com outro valor que não seja a propriedade do nome de .

* Causa:   
Valor inválido na propriedade do nome quando você cria um recurso.
* Solução:   
Deixe a propriedade do nome vazia ou deixe-a usar o mesmo valor que a propriedade do `resourceId`nome (entre o último backslash "/" e o ponto de interrogação "?") em .

***Tipo de protocolo {valor} não conhecido***

Este erro ocorre quando está a criar um volume com um tipo de protocolo desconhecido.  Os valores válidos são "NFSv3", "NFSv4" e "CIFS".

* Causa:   
Está a tentar definir um valor `protocolType` inválido na propriedade de volume.
* Solução:   
Coloque uma corda `protocolType`válida em .
* Solução:   
Definido `protocolType` como nulo.

***Os tipos de protocolo não podem ser alterados***

Este erro ocorre quando tenta atualizar `ProtocolType` ou remendar para obter um volume.  Alterar o ProtocoloType não é uma operação apoiada.

* Causa:   
A `ProtocolType` propriedade é definida quando o volume é criado.  Não pode ser atualizado.
* Solução:   
Nenhum.
* Solução:   
Crie outro volume com novos tipos de protocolos.

***A criação do recurso do tipo {resourceType} excederia a quota de recursos {quota} do tipo {resourceType} por {parentResourceType}. A contagem de recursos atual é {currentCount}, por favor elimine alguns recursos deste tipo antes de criar um novo.***

Este erro ocorre quando se está a`NetAppAccount` `CapacityPool`tentar `Volume`criar `Snapshot`um recurso , ou , mas a sua quota atingiu o seu limite.

* Causa:   
Está a tentar criar um recurso, mas o limite `NetAppAccounts` de `CapacityPools` quota `NetAppAccount`é atingido (exemplo: por subscrição ou por ).
* Solução:   
Aumente o limite de quota.
* Solução:   
Eliminar recursos não utilizados do mesmo tipo e criá-los novamente.

***Recebeu um valor para a propriedade apenas de leitura '{propertyName}'.***

Este erro ocorre quando se define um valor para uma propriedade que não pode ser alterada. Por exemplo, não é possível alterar o ID de volume.

* Causa:   
Está a tentar modificar um parâmetro (por exemplo, o ID de volume) que não pode ser alterado.
* Solução:   
Não modifique um valor para a propriedade.

***O {recurso} solicitado não foi encontrado.***

Este erro ocorre quando se tenta fazer referência a um recurso inexistente, por exemplo, um volume ou instantâneo. O recurso pode ter sido apagado ou ter um nome de recurso de misspelt.

* Causa:   
Está a tentar fazer referência a um recurso inexistente (por exemplo, um volume ou instantâneo) que já foi apagado ou tem um nome de recurso mal escrito.
* Solução:   
Verifique o pedido de erros ortográficos para se certificar de que está corretamente referenciado.
* Solução:   
Consulte a secção Solução acima.

***O nível de serviço '{volumeServiceLevel}' é superior ao dos pais '{poolServiceLevel}'***

Este erro ocorre quando está a criar ou atualizar um volume, e definiu o nível de serviço para um nível mais elevado do que o conjunto de capacidades que o contém.

* Causa:   
Está a tentar criar ou atualizar um volume com um nível de serviço mais elevado do que o conjunto de capacidade dos pais.
* Solução:   
Desloque o nível de serviço para o mesmo ou para um nível inferior ao da capacidade dos pais.
* Solução:   
Crie o volume em outra piscina de capacidade com um nível de serviço correto. Alternativamente, elimine todos os volumes do pool de capacidade e detetete tende o nível de serviço para o pool de capacidade para um nível mais elevado.

***O nome do servidor SMB pode não ser superior a 10 caracteres.***

Este erro ocorre quando está a criar ou atualizar uma configuração de Diretório Ativo para uma conta.

* Causa:   
O comprimento do nome do servidor SMB ultrapassa os 10 caracteres.
* Solução:   
Use um nome de servidor mais curto. O comprimento máximo é de 10 caracteres.
* Solução:   
Nenhum.  Veja a solução acima. 

***O SubnetId não pode ser alterado.***

Este erro ocorre quando se `subnetId` tenta alterar o volume após a criação do volume.  `SubnetId`deve ser definido quando o volume é criado e não pode ser alterado mais tarde.

* Causa:   
Está a tentar `subnetId` alterar o volume após a criação do volume, o que não é uma operação apoiada. 
* Solução:   
Se alterar `subnetId` o não é necessário, considere retirar o parâmetro do pedido de desminagem da mensagem de erro.
* Solução:   
Se precisar de `subnetId`alterar o , pode criar `subnetId`um novo volume com um novo , e depois migrar os dados para o novo volume.

***O SubnetId está em formato inválido.***

Este erro ocorre quando se tenta criar `subnetId` um novo `resourceId` volume, mas o não é um para uma sub-rede.

* Causa:   
Este erro ocorre quando se tenta criar um `subnetId` novo `resourceId` volume, mas o não é um para uma sub-rede. 
* Solução:   
Verifique o valor `subnetId` do que o `resourceId` deve certificar-se de que contém um para a sub-rede utilizada.
* Solução:   
Nenhum. Veja a solução acima. 

***A Subnet deve ter uma delegação 'Microsoft.NetApp/volumes'.***

Este erro ocorre quando está a criar um volume e `Microsoft.NetApp/volumes`a sub-rede selecionada não é delegada a .

* Causa:   
Tentou criar volume e selecionou uma sub-rede `Microsoft.NetApp/volumes`que não é delegada para .
* Solução:   
Selecione outra sub-rede `Microsoft.NetApp/volumes`que seja delegada em .
* Solução:   
Adicione uma delegação correta à subneta.

***O tipo de recurso especificado é desconhecido/não aplicável.***

Este erro ocorre quando uma verificação de nome foi solicitada, quer num tipo de recurso não aplicável, quer para um tipo de recurso desconhecido.

* Causa:   
Foi solicitada verificação de nomes para um tipo de recurso desconhecido ou não suportado.
* Solução:   
Verifique se o recurso para o que está a fazer é suportado ou não contém erros ortográficos.
* Solução:   
Veja a solução acima.

***Erro de ficheiros Azure NetApp desconhecidos.***

A API Azure conta com a API de Ficheiros Azure NetApp para gerir volumes. O erro indica um problema na comunicação à API.

* Causa:   
A API subjacente está a enviar um erro desconhecido. É provável que este erro seja temporário.
* Solução:   
É provável que a questão seja temporária e o pedido deverá ter êxito passado algum tempo. Se o problema persistir, crie um bilhete de apoio para que o problema seja investigado.
* Solução:   
Nenhum. A API subjacente é essencial para a gestão de volumes.

***Valor recebido por uma propriedade desconhecida '{propertyName}'.***

Este erro ocorre quando são fornecidas propriedades inexistentes para um recurso como o volume, instantâneo ou alvo de montagem.

* Causa:   
O pedido tem um conjunto de propriedades que podem ser usadas com cada recurso. Não pode incluir quaisquer propriedades inexistentes no pedido.
* Solução:   
Certifique-se de que todos os nomes de propriedade estão corretamente escritos e que as propriedades estão disponíveis para a subscrição e recurso.
* Solução:   
Reduza o número de propriedades definidas no pedido de eliminação da propriedade que está a causar o erro.

***A operação de atualização não é suportada para este tipo de recursos.***

Apenas volumes podem ser atualizados. Este erro ocorre quando tenta executar uma operação de atualização não suportada, por exemplo, atualizando um instantâneo.

* Causa:   
O recurso que está a tentar atualizar não suporta a operação de atualização. Apenas volumes podem ter as suas propriedades modificadas.
* Solução:   
Nenhum. O recurso que está a tentar atualizar não suporta a operação de atualização. Portanto, não pode ser alterado.
* Solução:   
Para um volume, crie um novo recurso com a atualização no lugar e emigra os dados.

***O volume não pode ser criado numa piscina que não está no estado bem sucedida.***

Este erro ocorre quando se tenta criar um volume numa piscina que não está no estado bem sucedido. Muito provavelmente, a operação de criação para o conjunto de capacidade falhou por alguma razão.

* Causa:   
O conjunto de capacidade que contém o novo volume encontra-se em estado de falência.
* Solução:   
Verifique se o pool de capacidade é criado com sucesso, e que não está em estado falhado.
* Solução:   
Crie uma nova piscina de capacidade e crie o volume na nova piscina.

***O volume está a ser criado e não pode ser eliminado neste momento.***

Este erro ocorre quando se tenta eliminar um volume que ainda está a ser criado.

* Causa:   
Ainda está a ser criado um volume quando se tenta apagar o volume.
* Solução:   
Aguarde até que a criação de volume esteja terminada e, em seguida, tente novamente a eliminação.
* Solução:   
Veja a solução acima.

***O volume está a ser eliminado e não pode ser eliminado neste momento.***

Este erro ocorre quando se tenta apagar um volume quando já está a ser apagado.

* Causa:   
Já está a ser apagado um volume quando se tenta apagar o volume.
* Solução:   
Aguarde até que a operação de eliminação atual esteja terminada.
* Solução:   
Veja a solução acima.

***O volume está a ser atualizado e não pode ser eliminado neste momento.***

Este erro ocorre quando se tenta eliminar um volume que está a ser atualizado.

* Causa:   
Está a ser atualizado um volume quando se tenta eliminar o volume.
* Solução:   
Aguarde até que a operação de atualização esteja terminada e, em seguida, tente novamente a eliminação.
* Solução:   
Veja a solução acima.

***O volume não foi encontrado ou não foi criado com sucesso.***

Este erro ocorre quando a criação de volume falhou e está a tentar alterar o volume ou criar um instantâneo para o volume.

* Causa:   
O volume não existe, ou a criação falhou.
* Solução:   
Verifique se está a alterar o volume correto e que a criação do volume foi bem sucedida. Ou, verifique se o volume para o que está a criar uma imagem instantânea existe.
* Solução:   
Nenhum.  Veja a solução acima. 

***Símbolo de criação especificado já existe***

Este erro ocorre quando se tenta criar um volume, e especifica-se um símbolo de criação (rota de exportação) para o qual já existe um volume.

* Causa:   
O símbolo de criação (rota de exportação) especificado durante a criação de volume já está associado a outro volume. 
* Solução:   
Escolha um símbolo de criação diferente.  Em alternativa, elimine o outro volume.

***Ficha de criação especificada é reservada***

Este erro ocorre quando se tenta criar um volume, e especifica "padrão" ou "nenhum" como o caminho do ficheiro (ficha de criação).

* Causa:    
Está a tentar criar um volume e especifica "padrão" ou "nenhum" como o caminho do ficheiro (símbolo de criação).
* Solução:   
Escolha um caminho de arquivo diferente (símbolo de criação).
 
***Credenciais de Diretório Ativo estão em uso***

Este erro ocorre quando se tenta eliminar a configuração do Diretório Ativo de uma conta onde ainda existe pelo menos um volume SMB.  O volume SMB foi criado utilizando a configuração ative diretório que está a tentar eliminar.

* Causa:   
Está a tentar eliminar a configuração do Diretório Ativo de uma conta, mas ainda existe pelo menos um volume SMB que foi inicialmente criado utilizando a configuração do Diretório Ativo. 
* Solução:   
Em primeiro lugar, elimine todos os volumes SMB que foram criados utilizando a configuração do Diretório Ativo.  Em seguida, tente novamente a eliminação da configuração.

***Não pode modificar a atribuição da Unidade Organizacional se as credenciais estiverem em uso***

Este erro ocorre quando se tenta alterar a Unidade Organizacional de uma configuração de Diretório Ativo, mas pelo menos um volume SMB ainda existe.  O volume SMB foi criado utilizando a configuração ative directy que está a tentar eliminar.

* Causa:   
Está a tentar alterar a Unidade Organizacional de uma configuração de Diretório Ativo.  Mas pelo menos um volume SMB ainda existe que foi inicialmente criado através da configuração do Diretório Ativo.
* Solução:   
 Em primeiro lugar, elimine todos os volumes SMB que foram criados utilizando a configuração do Diretório Ativo.  Em seguida, tente novamente a eliminação da configuração. 

***Atualização do Diretório Ativo já em curso***

Este erro ocorre quando se tenta editar uma configuração de Diretório Ativo para a qual já está em curso uma operação de edição.

* Causa:   
Está a tentar editar uma configuração de Diretório Ativo, mas outra operação de edição já está em curso.
* Solução:   
Aguarde até que a operação de edição em curso termine.

***Eliminar primeiro todos os volumes utilizando as credenciais selecionadas***

Este erro ocorre quando se tenta eliminar uma configuração de Diretório Ativo, mas pelo menos um volume SMB ainda existe.  O volume SMB foi criado utilizando a configuração ative diretório que está a tentar eliminar.

* Causa:   
Está a tentar eliminar uma configuração de Diretório Ativo, mas ainda existe pelo menos um volume SMB que foi inicialmente criado utilizando a configuração do Diretório Ativo.
* Solução:   
Em primeiro lugar, elimine todos os volumes SMB que foram criados utilizando a configuração do Diretório Ativo.  Em seguida, tente novamente a eliminação da configuração. 

***Nenhuma credencial de Diretório Ativo encontrada na região***

Este erro ocorre quando se tenta criar um volume SMB, mas não foi adicionada nenhuma configuração de Diretório Ativo à conta da região.

* Causa:   
Está a tentar criar um volume SMB, mas não foi adicionada nenhuma configuração de Diretório Ativo à conta. 
* Solução:   
Adicione uma configuração de Diretório Ativo à conta antes de criar um volume SMB.

***Não podia consultar o servidor DNS. Verifique se a configuração da rede está correta e que os servidores DNS estão disponíveis.***

Este erro ocorre quando tenta criar um volume SMB, mas um servidor DNS (especificado na configuração do Diretório Ativo) é inacessível. 

* Causa:   
Está a tentar criar um volume SMB, mas um servidor DNS (especificado na configuração do Diretório Ativo) é inacessível.
* Solução:   
Reveja a configuração do Diretório Ativo e certifique-se de que os endereços IP do servidor DNS estão corretos e acessíveis.
Se não houver problemas com os endereços IP do servidor DNS, verifique se nenhuma firewall está bloqueando o acesso.

***Demasiados empregos simultâneos***

Este erro ocorre quando se tenta criar um instantâneo quando já estão em curso três outras operações de criação instantânea para a subscrição.

* Causa:   
Está a tentar criar um instantâneo quando já estão em curso três outras operações de criação instantânea para a subscrição. 
* Solução:   
Os trabalhos de criação instantânea demoram alguns segundos a terminar.  Aguarde alguns segundos e tente novamente a operação de criação instantânea.

***Não pode criar trabalhos adicionais. Por favor, espere que os trabalhos em curso terminem e tentem novamente***

Este erro pode ocorrer quando tenta criar ou apagar um volume em circunstâncias específicas.

* Causa:   
Está a tentar criar ou apagar um volume em circunstâncias específicas.
* Solução:   
Espere um minuto ou mais e tente novamente a operação.

***O volume já está em transição entre estados***

Este erro pode ocorrer quando se tenta eliminar um volume que se encontra atualmente em estado de transição (isto é, atualmente no estado de criação, atualização ou eliminação).

* Causa:   
Está a tentar apagar um volume que se encontra atualmente em estado de transição.
* Solução:   
Aguarde até que a operação atualmente em curso (transição do Estado) esteja concluída e, em seguida, tente novamente a operação.

***Não conseguiu dividir o novo volume do instantâneo de volume de origem***

 Este erro pode ocorrer quando se tenta criar um volume a partir de um instantâneo.  

* Causa:   
Tenta criar um volume a partir de um instantâneo e o volume termina num estado de erro.
* Solução:   
Apagar o volume e, em seguida, voltar a tentar a operação de criação de volume a partir do instantâneo.

 
## <a name="next-steps"></a>Passos seguintes

* [Desenvolver ficheiros Azure NetApp com REST API](azure-netapp-files-develop-with-rest-api.md)
