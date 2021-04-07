---
title: Resolução de problemas Azure NetApp Ficheiros Recursos Provedor de Recursos | Microsoft Docs
description: Descreve causas, soluções e soluções alternativas para erros comuns do Fornecedor de Recursos de Ficheiros Azure NetApp.
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
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: b-juche
ms.openlocfilehash: ac83e5a9366a12c5adce0e08f04f2bb28a7d788d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374886"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Resolver problemas de erros do Fornecedor de Recursos do Azure NetApp Files 

Este artigo descreve erros comuns do Fornecedor de Recursos de Ficheiros Azure NetApp, as suas causas, soluções e soluções alternativas (se disponível).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Erros comuns do Fornecedor de Recursos de Ficheiros Azure NetApp

***A criação `netAppAccounts` foi restringida nesta região.***

Esta situação ocorre quando a subscrição está na lista de espera para ficheiros Azure NetApp e o utilizador tenta criar uma conta NetApp.

* Causa:   
O Fornecedor de Recursos Azure para ficheiros Azure NetApp não está registado com sucesso. 
 
* Solução:   
Complete todos os passos descritos no [registo do fornecedor de recursos Azure NetApp](azure-netapp-files-register.md#resource-provider) após a sua subscrição estar na lista de espera.

***O BareMetalTenantId não pode ser alterado.***  

Este erro ocorre quando tenta atualizar ou corrigir um volume e a `BaremetalTenantId` propriedade tem um valor alterado.

* Causa:   
Está a tentar atualizar um volume e a `BaremetalTenantId` propriedade tem um valor diferente do valor armazenado em Azure.
* Solução:   
Não inclua `BaremetalTenantId` no patch e pedido de atualização (colocado). Em alternativa, certifique-se `BaremetalTenantId` de que é o mesmo no pedido.

***ServiçoLevel não pode ser alterado.***  

Este erro ocorre quando se tenta atualizar ou corrigir um pool de capacidade com um nível de serviço diferente quando o pool de capacidade já tem volumes.

* Causa:   
Está a tentar atualizar um nível de serviço de pool de capacidade quando o pool contém volumes.
* Solução:   
Elimine todos os volumes do pool de capacidade e, em seguida, altere o nível de serviço.
* Solução:   
Crie outra piscina de capacidade e, em seguida, crie os volumes novamente no novo pool de capacidade.

***PoolId não pode ser alterado***  

Este erro ocorre quando tenta atualizar ou corrigir um pool de capacidade com uma `PoolId` propriedade alterada.

* Causa:   
Você está tentando atualizar uma propriedade de piscina de `PoolId` capacidade. A `PoolId` propriedade é uma propriedade só de leitura e não pode ser alterada.
* Solução:   
Não inclua `PoolId` no patch e pedido de atualização (colocado).  Em alternativa, certifique-se `PoolId` de que é o mesmo no pedido.

***CriaçãoToken não pode ser alterada.***

Este erro ocorre quando se tenta alterar a trajetória do ficheiro `CreationToken` () depois de ter sido criado o volume. O caminho do ficheiro `CreationToken` () deve ser definido quando o volume é criado, e não pode ser alterado mais tarde.

* Causa:   
Está a tentar alterar o percurso do ficheiro `CreationToken` depois de ter sido criado o volume, o que não é uma operação suportada. 
* Solução:   
Se não for necessário alterar o caminho do ficheiro, considere remover o parâmetro do pedido para rejeitar a mensagem de erro.
* Solução:   
Se precisar de alterar o caminho do ficheiro , `CreationToken` pode criar um novo volume com uma nova trajetória de ficheiro e, em seguida, migrar os dados para o novo volume.

***CreationToken deve ter pelo menos 16 caracteres de comprimento.***

Este erro ocorre quando a trajetória do ficheiro `CreationToken` () não satisfaz o requisito de comprimento. O comprimento do percurso do ficheiro deve ter pelo menos um carácter de comprimento.

* Causa:   
O caminho do ficheiro está vazio.  Quando se cria um volume utilizando a API, é necessário um símbolo de criação. Se estiver a utilizar o portal Azure, o caminho do ficheiro é gerado automaticamente.
* Solução:   
Introduza pelo menos um personagem como o caminho do ficheiro `CreationToken` ().

***O nome de domínio não pode ser alterado.***

Este erro ocorre quando se tenta alterar o nome de domínio no Ative Directory.

* Causa:   
Está a tentar atualizar a propriedade do nome de domínio.
* Solução:    
Nenhum. Não é possível alterar o nome de domínio.
* Solução:   
Elimine todos os volumes utilizando a configuração do Ative Directory. Em seguida, elimine a configuração do Ative Directory e re-crie os volumes.

***Duplicar o erro de valor para objeto ExportPolicy.Rules[RuleIndex].***

Este erro ocorre quando a política de exportação não é definida com um índice único. Quando se definem as políticas de exportação, todas as regras da política de exportação devem ter um índice único entre 1 e 5.

* Causa:   
A política de exportação definida não satisfaz os requisitos em vigor das regras relativas à política de exportação. Deve ter uma regra de política de exportação no mínimo e cinco regras de política de exportação no máximo.
* Solução:   
Certifique-se de que o índice ainda não está utilizado e que está na faixa de 1 a 5.
* Solução:   
Use um índice diferente para a regra que está a tentar definir.

***Erro {ação} {recursoTypeName}***

Este erro é apresentado quando outros erros não manusearam o erro enquanto executavam uma ação num recurso.   Inclui texto 'Erro'. `{action}`Pode ser qualquer um de , , , ou `getting` `creating` `updating` `deleting` .  O `{resourceTypeName}` é o `resourceTypeName` (por exemplo, `netAppAccount` , , , e assim por `capacityPool` `volume` diante).

* Causa:   
Este erro é uma exceção não manipulada quando a causa não é conhecida.
* Solução:   
Contacte o Centro de Apoio Azure para informar a razão detalhada nos registos.
* Solução:   
Nenhum.

***O nome do caminho do ficheiro pode conter apenas letras, números e hífens ("") apenas.***

Este erro ocorre quando o caminho do ficheiro contém caracteres não suportados, por exemplo, um período ("."), vírgula (""), sublinhar ("_"), ou sinal de dólar ("$").

* Causa:   
O caminho do ficheiro contém caracteres não suportados, por exemplo, um período ("."), vírgula (""), sublinhar ("_"), ou sinal de dólar ("$").
* Solução:   
Remova caracteres que não sejam letras alfabéticas, números ou hífens ("-") do caminho de ficheiro que inseriu.
* Solução:   
Pode substituir um sublinhado por um hífen ou utilizar a capitalização em vez de espaços que indiquem o início de novas palavras.  Por exemplo, utilize "NewVolume" em vez de "novo volume".

***O FileSystemId não pode ser alterado.***

Este erro ocorre quando se tenta mudar `FileSystemId` .  Mudar `FileSystemdId` não é uma operação apoiada. 

* Causa:   
O ID do sistema de ficheiros é definido quando o volume é criado. `FileSystemId` não pode ser alterado posteriormente.
* Solução:   
Não inclua `FileSystemId` um pedido de patch e atualização (colocado).  Em alternativa, certifique-se de que `FileSystemId` é o mesmo no pedido.

***ActiveDirectory com id: '{string}' não existe.***

A `{string}` parte é o valor que inseriu na propriedade para a `ActiveDirectoryId` ligação Ative Directory.

* Causa:   
Quando criou uma conta com a configuração do Ative Directory, introduziu um valor para `ActiveDirectoryId` que deveria estar vazio.
* Solução:   
Não inclua `ActiveDirectoryId` no pedido de criação (colocar).

***Versão api inválida.***

A versão API não é submetida ou contém um valor inválido.

* Causa:   
O valor no parâmetro de consulta `api-version` contém um valor inválido.
* Solução:   
Use o valor correto da versão API.  O fornecedor de recursos suporta muitas versões API. O valor está no formato de yyy-mm-dd.

***Foi recebido um valor inválido '{value}' para {1} .***

Esta mensagem indica um erro nos campos `RuleIndex` `AllowedClients` para, `UnixReadOnly` , , `UnixReadWrite` e `Nfsv3` `Nfsv4` .

* Causa:   
O pedido de validação de entradas falhou em pelo menos um dos seguintes campos: `RuleIndex` `AllowedClients` , , , `UnixReadOnly` `UnixReadWrite` `Nfsv` 3, e `Nfsv4` .
* Solução:   
Certifique-se de definir todos os parâmetros necessários e não conflitos na linha de comando. Por exemplo, não é possível definir os `UnixReadOnly` parâmetros e `UnixReadWrite` os parâmetros ao mesmo tempo.
* Solução:   
Veja a solução acima.

***O intervalo de IP {0} {1} para vlan já está em {2} uso***

Este erro ocorre porque os registos internos dos intervalos IP usados têm um conflito com o endereço IP recentemente atribuído.

* Causa:   
O endereço IP atribuído para a criação de volume já está registado.
A razão pode ser uma criação de volume falhada anterior.
* Solução:   
Contacte o Centro de Apoio Azure.

***Valor em falta para '{property}'.***

Este erro indica que falta uma propriedade requerida no pedido. A cadeia {propriedade} contém o nome da propriedade em falta.

* Causa:   
O pedido de validação de entrada falhou em pelo menos uma das propriedades.
* Solução:   
Certifique-se de definir todas as propriedades necessárias e não conflitos no pedido, especialmente, a propriedade a partir da mensagem de erro.

***Os MountTargets não podem ser alterados.***

Este erro ocorre quando um utilizador está a tentar atualizar ou corrigir o volume que o MountTargets tem.

* Causa:   
Está a tentar atualizar a propriedade de `MountTargets` volume. A alteração desta propriedade não é suportada.
* Solução:   
Não inclua `MountTargets` um pedido de patch e atualização (colocado).  Em alternativa, certifique-se de que `MountTargets` é o mesmo no pedido.

***Nome já em uso.***

Este erro indica que o nome do recurso já está a ser utilizado.

* Causa:   
Está a tentar criar um recurso com um nome que é usado para um recurso existente.
* Solução:   
Utilize um nome único ao criar o recurso.

***Percurso de arquivo já em uso.***

Este erro indica que o caminho do ficheiro para o volume já está a ser utilizado.

* Causa:   
Está a tentar criar um volume com um caminho de ficheiro que seja o mesmo que um volume existente.
* Solução:   
Utilize um caminho de ficheiro único ao criar o volume.

***Nome muito comprido.***

Este erro indica que o nome do recurso não satisfaz o requisito máximo de comprimento.

* Causa:   
O nome do recurso é muito longo.
* Solução:   
Utilize um nome mais curto para o recurso.

***O caminho do arquivo é muito longo.***

Este erro indica que a trajetória do ficheiro para o volume não satisfaz o requisito máximo de comprimento.

* Causa:   
O percurso do ficheiro de volume é demasiado longo.
* Solução:   
Use um caminho de arquivo mais curto.

***Nome muito curto.***

Este erro indica que o nome do recurso não satisfaz o requisito mínimo de comprimento.

* Causa:   
O nome do recurso é muito curto.
* Solução:   
Use um nome mais comprido para o recurso.

***O caminho do ficheiro é muito curto.***

Este erro indica que a trajetória do ficheiro de volume não satisfaz o requisito mínimo de comprimento.

* Causa:   
O percurso do ficheiro de volume é muito curto.
* Solução:   
Aumente o comprimento da trajetória do ficheiro de volume.

***Azure NetApp Files API inacessível.***

A Azure API conta com a Azure NetApp Files API para gerir volumes. Este erro indica um problema com a ligação API.

* Causa:   
A API subjacente não responde, resultando num erro interno. Este erro é suscetível de ser temporário.
* Solução:   
É provável que a questão seja temporária. O pedido deve ter sucesso depois de algum tempo.
* Solução:   
Nenhum. A API subjacente é essencial para a gestão de volumes.

***Nenhuma identificação de resultado de operação encontrada para {0} '***

Este erro indica que um erro interno está a impedir que o funcionamento esteja concluído.

* Causa:   
Ocorreu um erro interno e impediu que a operação se completasse.
* Solução:   
Este erro é suscetível de ser temporário. Aguarde alguns minutos e tente novamente. Se o problema persistir, crie um bilhete para que o apoio técnico investigue o assunto.
* Solução:   
Aguarde alguns minutos e verifique se o problema persiste.

***Não é permitido misturar tipos de protocolo CIFS e NFS***

Este erro ocorre quando está a tentar criar um Volume e existem os tipos de protocolo CIFS (SMB) e NFS nas propriedades de volume.

* Causa:   
Tanto os tipos de protocolo CIFS (SMB) como NFS são utilizados nas propriedades de volume.
* Solução:   
Remova um dos tipos de protocolo.
* Solução:   
Deixe a propriedade tipo protocolo vazia ou nula.

***Número de itens: {value} para objeto: ExportPolicy.Rules[RuleIndex] está fora da gama min-max.***

Este erro ocorre quando as regras da política de exportação não cumprem o requisito de gama mínima ou máxima. Se definirmos a política de exportação, ela deve ter uma regra de política de exportação no mínimo e cinco regras de política de exportação no máximo.

* Causa:   
A política de exportação que definiu não corresponde ao intervalo exigido.
* Solução:   
Certifique-se de que o índice ainda não está utilizado e que está na faixa de 1 a 5.
* Solução:   
Não é obrigatório utilizar a política de exportação nos volumes. Pode omitir totalmente a política de exportação se não precisar de utilizar as regras da política de exportação.

***Apenas um diretório ativo permitido***

Este erro ocorre quando se tenta criar uma configuração ative directory, e já existe para a subscrição na região. O erro também pode ocorrer quando tenta criar mais do que uma configuração do Ative Directory.

* Causa:   
Está a tentar criar (não atualizar) um diretório ativo, mas um já existe.
* Solução:   
Se a configuração do Diretório Ativo não estiver a ser utilizada, pode primeiro eliminar a configuração existente e, em seguida, voltar a tentar a operação de criação.
* Solução:   
Nenhum. Só é permitido um Diretório Ativo.

***Operação '{operation}' não suportada.***

Este erro indica que a operação não está disponível para a subscrição ou recurso ativo.

* Causa:   
A operação não está disponível para a subscrição ou recurso.
* Solução:   
Certifique-se de que a operação está corretamente inserida e que está disponível para o recurso e subscrição que está a utilizar.

***ProprietárioId não pode ser alterado***

Este erro ocorre quando tenta alterar a propriedade OwnerId do volume. Mudar o OwnerId não é uma operação suportada. 

* Causa:   
A `OwnerId` propriedade é definida quando o volume é criado. A propriedade não pode ser alterada posteriormente.
* Solução:   
Não inclua `OwnerId` um pedido de patch e atualização (colocado). Em alternativa, certifique-se de que `OwnerId` é o mesmo no pedido.

***Piscina dos pais não encontrada***

Este erro ocorre quando se tenta criar um volume e a capacidade em que está a criar o volume não é encontrado.

* Causa:   
Não se encontra a capacidade de criação do volume.
* Solução:   
Muito provavelmente a piscina não foi totalmente criada ou já foi apagada no momento da criação do volume.

***O funcionamento do patch não é suportado para este tipo de recurso.***

Este erro ocorre quando se tenta alterar o alvo de montagem ou o instantâneo.

* Causa:   
O alvo de montagem é definido quando é criado, e não pode ser alterado posteriormente.
As fotos não contêm propriedades que possam ser alteradas.
* Solução:   
Nenhum. Esses recursos não têm propriedades que possam ser alteradas.

***Tamanho da piscina muito pequeno para o tamanho total do volume.***

Este erro ocorre quando está a atualizar o tamanho da piscina de capacidade, e o tamanho é menor do que o valor total `usedBytes` de todos os volumes nessa capacidade.  Este erro também pode ocorrer quando está a criar um novo volume ou a redimensionar um volume existente, e o novo tamanho de volume excede o espaço livre na piscina de capacidade.

* Causa:   
Está a tentar atualizar a piscina de capacidade para um tamanho menor do que o usadoBytes em todos os volumes da piscina de capacidade.  Ou, você está tentando criar um volume que é maior do que o espaço livre na piscina de capacidade.  Em alternativa, está a tentar redimensionar um volume e o novo tamanho excede o espaço livre na piscina de capacidade.
* Solução:   
Desa ajuste o tamanho da piscina de capacidade para um valor maior, ou crie um volume menor para um volume.
* Solução:   
Remova volumes suficientes para que o tamanho da piscina possa ser atualizado para este tamanho.

***A propriedade: A localização para snapshot deve ser a mesma que volume***

Este erro ocorre quando está a criar uma imagem com uma localização diferente do volume que possui o instantâneo.

* Causa:   
Valor inválido na propriedade Localização para o instantâneo.
* Solução:   
Desa parte da cadeia válida na propriedade Localização.

***O nome {resourceType} deve ser o mesmo que o nome do identificador de recursos.***

Este erro ocorre quando está a criar um recurso, e preenche a propriedade do nome com outro valor que não o nome de propriedade de `resourceId` .

* Causa:   
Valor inválido na propriedade do nome quando cria um recurso.
* Solução:   
Deixe a propriedade do nome vazia ou permita-lhe usar o mesmo valor que a propriedade do nome (entre o último backslash "/" e o ponto de interrogação "?") em `resourceId` .

***Tipo de protocolo {valor} não conhecido***

Este erro ocorre quando está a criar um volume com um tipo de protocolo desconhecido.  Os valores válidos são "NFSv3", "NFSv4" e "CIFS".

* Causa:   
Está a tentar definir um valor inválido na propriedade de `protocolType` volume.
* Solução:   
Coloque uma corda válida em `protocolType` .
* Solução:   
Definido `protocolType` como nulo.

***Os tipos de protocolo não podem ser alterados***

Este erro ocorre quando se tenta atualizar ou corrigir `ProtocolType` um volume.  Alterar o ProtocoloType não é uma operação suportada.

* Causa:   
A `ProtocolType` propriedade é definida quando o volume é criado.  Não pode ser atualizado.
* Solução:   
Nenhum.
* Solução:   
Criar outro volume com novos tipos de protocolo.

***A criação do recurso do tipo {resourceType} excederia a quota de recursos {quota} do tipo {resourceType} por {parentResourceType}. A contagem atual de recursos é {currentCount}, por favor, elimine alguns recursos deste tipo antes de criar um novo.***

Este erro ocorre quando se está a tentar criar um recurso ( `NetAppAccount` , , ou , ou ), mas a sua quota `CapacityPool` `Volume` `Snapshot` atingiu o seu limite.

* Causa:   
Está a tentar criar um recurso, mas o limite de quota é atingido (exemplo: `NetAppAccounts` por subscrição ou `CapacityPools` por `NetAppAccount` ).
* Solução:   
Aumente o limite de quota.
* Solução:   
Elimine os recursos não utilizados do mesmo tipo e crie-os novamente.

***Recebeu um valor para propriedade apenas de leitura '{propertyName}'.***

Este erro ocorre quando se define um valor para um imóvel que não pode ser alterado. Por exemplo, não é possível alterar o ID do volume.

* Causa:   
Está a tentar modificar um parâmetro (por exemplo, o ID de volume) que não pode ser alterado.
* Solução:   
Não modifique um valor para o imóvel.

***O {recurso solicitado} não foi encontrado.***

Este erro ocorre quando se tenta fazer referência a um recurso inexistente, por exemplo, um volume ou instantâneo. O recurso pode ter sido apagado ou ter um nome de recurso de misspelt.

* Causa:   
Está a tentar fazer referência a um recurso inexistente (por exemplo, um volume ou instantâneo) que já foi apagado ou tem um nome de recurso mal escrito.
* Solução:   
Verifique o pedido de erros ortográficos para se certificar de que está corretamente referenciado.
* Solução:   
Consulte a secção Solução acima.

***O nível de serviço '{volumeServiceLevel}' é superior ao dos pais '{poolServiceLevel}'***

Este erro ocorre quando está a criar ou atualizar um volume, e definiu o nível de serviço para um nível mais elevado do que o pool de capacidade que o contém.

* Causa:   
Está a tentar criar ou atualizar um volume com um nível de serviço mais elevado do que o pool de capacidade dos pais.
* Solução:   
Desa um nível de serviço inferior ao do conjunto de capacidade dos pais.
* Solução:   
Crie o volume em outra piscina de capacidade com um nível de serviço correto. Em alternativa, elimine todos os volumes do pool de capacidade e desemote o nível de serviço para o pool de capacidade para um nível mais elevado.

***O nome do servidor SMB pode não ter mais de 10 caracteres.***

Este erro ocorre quando está a criar ou atualizar uma configuração do Ative Directory para uma conta.

* Causa:   
O comprimento do nome do servidor SMB excede 10 caracteres.
* Solução:   
Use um nome de servidor mais curto. O comprimento máximo é de 10 caracteres.
* Solução:   
Nenhum.  Veja a solução acima. 

***O subnetId não pode ser alterado.***

Este erro ocorre quando se tenta alterar o `subnetId` volume após a criação do volume.  `SubnetId` deve ser definido quando o volume é criado e não pode ser alterado mais tarde.

* Causa:   
Está a tentar alterar o `subnetId` volume após a criação do volume, o que não é uma operação apoiada. 
* Solução:   
Se alterar o `subnetId` não é necessário, considere remover o parâmetro do pedido para rejeitar a mensagem de erro.
* Solução:   
Se precisar de alterar o `subnetId` volume, pode criar um novo volume com um novo `subnetId` , e depois migrar os dados para o novo volume.

***SubnetId está em formato inválido.***

Este erro ocorre quando se tenta criar um novo volume, mas `subnetId` o não é para uma `resourceId` sub-rede.

* Causa:   
Este erro ocorre quando se tenta criar um novo volume, mas `subnetId` o não é para uma `resourceId` sub-rede. 
* Solução:   
Verifique o valor `subnetId` para garantir que contém uma para a `resourceId` sub-rede utilizada.
* Solução:   
Nenhum. Veja a solução acima. 

***A sub-rede deve ter uma delegação 'Microsoft.NetApp/volumes'.***

Este erro ocorre quando está a criar um volume e a sub-rede selecionada não é delegada em `Microsoft.NetApp/volumes` .

* Causa:   
Tentou criar volume e selecionou uma sub-rede que não está delegada em `Microsoft.NetApp/volumes` .
* Solução:   
Selecione outra sub-rede que é delegada em `Microsoft.NetApp/volumes` .
* Solução:   
Adicione uma delegação correta à sub-rede.

***O tipo de recurso especificado é desconhecido/não aplicável.***

Este erro ocorre quando uma verificação de nomes foi solicitada quer num tipo de recurso não aplicável, quer para um tipo de recurso desconhecido.

* Causa:   
Foi solicitada a verificação de nomes para um tipo de recurso desconhecido ou não suportado.
* Solução:   
Verifique se o recurso que está a fazer o pedido é suportado ou não contém erros ortográficos.
* Solução:   
Veja a solução acima.

***Erro de ficheiros Azure NetApp desconhecido.***

A Azure API conta com a Azure NetApp Files API para gerir volumes. O erro indica um problema na comunicação à API.

* Causa:   
A API subjacente está a enviar um erro desconhecido. Este erro é suscetível de ser temporário.
* Solução:   
É provável que a questão seja temporária, e o pedido deverá ter êxito ao fim de algum tempo. Se o problema persistir, crie um bilhete de apoio para que o assunto fosse investigado.
* Solução:   
Nenhum. A API subjacente é essencial para a gestão de volumes.

***Valor recebido por uma propriedade desconhecida '{propertyName}'.***

Este erro ocorre quando propriedades inexistentes são fornecidas para um recurso como o volume, instantâneo ou alvo de montagem.

* Causa:   
O pedido tem um conjunto de propriedades que podem ser usadas com cada recurso. Não pode incluir nenhuma propriedade inexistente no pedido.
* Solução:   
Certifique-se de que todos os nomes de propriedade estão escritos corretamente e que as propriedades estão disponíveis para a subscrição e recurso.
* Solução:   
Reduza o número de imóveis definidos no pedido de eliminação do imóvel que está a causar o erro.

***A operação de atualização não é suportada para este tipo de recurso.***

Apenas volumes podem ser atualizados. Este erro ocorre quando tenta realizar uma operação de atualização não suportada, por exemplo, atualizando uma imagem instantânea.

* Causa:   
O recurso que está a tentar atualizar não suporta a operação de atualização. Apenas volumes podem ter as suas propriedades modificadas.
* Solução:   
Nenhum. O recurso que está a tentar atualizar não suporta a operação de atualização. Portanto, não pode ser alterado.
* Solução:   
Para um volume, crie um novo recurso com a atualização no lugar e migrar os dados.

***O volume não pode ser criado numa piscina que não está no estado bem sucedida.***

Este erro ocorre quando se tenta criar um volume numa piscina que não esteja no estado de sucesso. Provavelmente, a operação de criação para o pool de capacidade falhou por alguma razão.

* Causa:   
A capacidade de piscina que contém o novo volume encontra-se num estado de falha.
* Solução:   
Verifique se o pool de capacidade é criado com sucesso e que não está em estado de falha.
* Solução:   
Crie uma nova piscina de capacidade e crie o volume na nova piscina.

***O volume está a ser criado e não pode ser eliminado neste momento.***

Este erro ocorre quando se tenta eliminar um volume que ainda está a ser criado.

* Causa:   
Ainda está a ser criado um volume quando se tenta apagar o volume.
* Solução:   
Aguarde até que a criação de volume esteja terminada e, em seguida, re-tentar a supressão.
* Solução:   
Veja a solução acima.

***O volume está a ser apagado e não pode ser apagado neste momento.***

Este erro ocorre quando se tenta eliminar um volume quando já está a ser eliminado.

* Causa:   
Um volume já está a ser apagado quando se tenta apagar o volume.
* Solução:   
Aguarde até que a operação de eliminação atual esteja terminada.
* Solução:   
Veja a solução acima.

***O volume está a ser atualizado e não pode ser eliminado neste momento.***

Este erro ocorre quando se tenta eliminar um volume que está a ser atualizado.

* Causa:   
Um volume está a ser atualizado quando se tenta eliminar o volume.
* Solução:   
Aguarde até que a operação de atualização esteja terminada e, em seguida, re-tentar a supressão.
* Solução:   
Veja a solução acima.

***O volume não foi encontrado ou não foi criado com sucesso.***

Este erro ocorre quando a criação de volume falhou e está a tentar alterar o volume ou criar uma imagem instantânea para o volume.

* Causa:   
O volume não existe, ou a criação falhou.
* Solução:   
Verifique se está a alterar o volume correto e que a criação do volume foi bem sucedida. Ou, verifique se o volume para o que está a criar existe uma imagem instantânea.
* Solução:   
Nenhum.  Veja a solução acima. 

***Token de criação especificado já existe***

Este erro ocorre quando se tenta criar um volume, e especifica um token de criação (caminho de exportação) para o qual já existe um volume.

* Causa:   
O token de criação (caminho de exportação) especificado durante a criação de volume já está associado a outro volume. 
* Solução:   
Escolha um símbolo de criação diferente.  Em alternativa, elimine o outro volume.

***O símbolo de criação especificado é reservado***

Este erro ocorre quando tenta criar um volume e especifica "predefinido" ou "nenhum" como o caminho do ficheiro (token de criação).

* Causa:    
Está a tentar criar um volume e especifica "predefinido" ou "nenhum" como o caminho do ficheiro (símbolo de criação).
* Solução:   
Escolha um caminho de arquivo diferente (símbolo de criação).
 
***Credenciais de diretório ativo estão em uso***

Este erro ocorre quando se tenta eliminar a configuração do Ative Directory de uma conta onde ainda existe pelo menos um volume SMB.  O volume SMB foi criado utilizando a configuração ative directory que está a tentar eliminar.

* Causa:   
Está a tentar eliminar a configuração do Ative Directory de uma conta, mas ainda existe pelo menos um volume SMB que foi inicialmente criado utilizando a configuração do Ative Directory. 
* Solução:   
Em primeiro lugar, elimine todos os volumes SMB que foram criados utilizando a configuração do Ative Directory.  Em seguida, re-tentar a eliminação da configuração.

***Não é possível modificar a atribuição da Unidade Organizacional se as credenciais estiverem a ser utilizadas***

Este erro ocorre quando se tenta alterar a Unidade Organizacional de uma configuração de Diretório Ativo, mas pelo menos um volume SMB ainda existe.  O volume SMB foi criado utilizando a configuração ative directory que está a tentar eliminar.

* Causa:   
Está a tentar alterar a Unidade Organizacional de uma configuração do Diretório Ativo.  Mas pelo menos um volume SMB ainda existe que foi inicialmente criado usando a configuração do Ative Directory.
* Solução:   
 Em primeiro lugar, elimine todos os volumes SMB que foram criados utilizando a configuração do Ative Directory.  Em seguida, re-tentar a eliminação da configuração. 

***Atualização do Diretório Ativo já em curso***

Este erro ocorre quando se tenta editar uma configuração ative directory para a qual já está em curso uma operação de edição.

* Causa:   
Está a tentar editar uma configuração do Ative Directory, mas já está em curso outra operação de edição.
* Solução:   
Aguarde até que a operação de edição atualmente em execução tenha terminado.

***Eliminar todos os volumes usando as credenciais selecionadas primeiro***

Este erro ocorre quando se tenta eliminar uma configuração do Ative Directory, mas pelo menos um volume SMB ainda existe.  O volume SMB foi criado utilizando a configuração ative directory que está a tentar eliminar.

* Causa:   
Está a tentar eliminar uma configuração do Ative Directory, mas ainda existe pelo menos um volume SMB que foi inicialmente criado utilizando a configuração do Ative Directory.
* Solução:   
Em primeiro lugar, elimine todos os volumes SMB que foram criados utilizando a configuração do Ative Directory.  Em seguida, re-tentar a eliminação da configuração. 

***Não são encontradas credenciais de Diretório Ativo na região***

Este erro ocorre quando se tenta criar um volume SMB, mas não foi adicionada nenhuma configuração do Ative Directory à conta da região.

* Causa:   
Está a tentar criar um volume SMB, mas não foi adicionada nenhuma configuração do Ative Directory à conta. 
* Solução:   
Adicione uma configuração ative diretoria à conta antes de criar um volume SMB.

***Não foi possível consultar o servidor DNS. Verifique se a configuração da rede está correta e se os servidores DNS estão disponíveis.***

Este erro ocorre quando se tenta criar um volume SMB, mas um servidor DNS (especificado na configuração do Diretório Ativo) é inacessível. 

* Causa:   
Está a tentar criar um volume SMB, mas um servidor DNS (especificado na configuração do Diretório Ativo) é inacessível.
* Solução:   
Reveja a configuração do Ative Directory e certifique-se de que os endereços IP do servidor DNS estão corretos e contactáveis.
Se não houver problemas com os endereços IP do servidor DNS, verifique se não há firewalls que bloqueiem o acesso.

***Demasiados empregos simultâneos***

Este erro ocorre quando se tenta criar um instantâneo quando três outras operações de criação instantânea já estão em andamento para a subscrição.

* Causa:   
Está a tentar criar um instantâneo quando três outras operações de criação instantânea já estão em andamento para a subscrição. 
* Solução:   
Os trabalhos de criação instantânea demoram alguns segundos, no máximo, a terminar.  Aguarde alguns segundos e relemisça a operação de criação instantânea.

***Não pode criar empregos adicionais. Por favor, espere que os trabalhos em curso terminem e tente de novo.***

Este erro pode ocorrer quando se tenta criar ou apagar um volume em circunstâncias específicas.

* Causa:   
Está a tentar criar ou apagar um volume em circunstâncias específicas.
* Solução:   
Espere um minuto ou mais e relemisça a operação.

***O volume já está em transição entre estados***

Este erro pode ocorrer quando se tenta eliminar um volume que se encontra atualmente num estado de transição (isto é, atualmente, no estado de criação, atualização ou eliminação).

* Causa:   
Está a tentar apagar um volume que se encontra atualmente num estado de transição.
* Solução:   
Aguarde até que a operação atualmente em execução (transição do estado) tenha terminado e, em seguida, re-tentar a operação.

***Não conseguiu dividir novo volume a partir do instantâneo de volume de origem***

 Este erro pode ocorrer quando se tenta criar um volume a partir de uma imagem instantânea.  

* Causa:   
Tenta-se criar um volume a partir de um instantâneo e o volume termina num estado de erro.
* Solução:   
Elimine o volume e, em seguida, redoça a operação de criação de volume a partir do instantâneo.

 
## <a name="next-steps"></a>Passos seguintes

* [Desenvolver para o Azure NetApp Files com a API REST](azure-netapp-files-develop-with-rest-api.md)
