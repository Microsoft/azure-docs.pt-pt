---
title: O que é o Dedicated HSM? - Azure Dedicado HSM [ HSM ] Microsoft Docs
description: Visão geral do Azure Dedicado HSM fornece principais capacidades de armazenamento dentro do Azure que cumpre a certificação FIPS 140-2 Nível 3
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ad3c9d702384b8a32a9d4f0c8aebe44de4bb526e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80337177"
---
# <a name="troubleshooting"></a>Resolução de problemas

O serviço Azure Dedicado HSM tem duas facetas distintas. Em primeiro lugar, o registo e a implantação em Azure dos dispositivos HSM com os seus componentes de rede subjacentes. Em segundo lugar, a configuração dos dispositivos HSM em preparação para a utilização/integração com uma determinada carga de trabalho ou aplicação. Embora os dispositivos HSM da Rede Thales Luna sejam os mesmos em Azure que compraria diretamente à Thales, o facto de serem um recurso no Azure cria algumas considerações únicas. Estas considerações e quaisquer insights de resolução de problemas resultantes ou boas práticas, são documentados aqui para garantir alta visibilidade e acesso a informações críticas. Uma vez que o serviço está em uso, informações definitivas estão disponíveis através de pedidos de suporte para Microsoft ou Thales diretamente. 

> [!NOTE]
> Note-se que antes de realizar qualquer configuração num dispositivo HSM recentemente implantado, este deve ser atualizado com quaisquer patches relevantes. Um patch específico exigido é [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) no portal de suporte thales que aborda um problema de reinicialização.

## <a name="hsm-registration"></a>Registo HSM

O HSM dedicado não está livremente disponível para uso, uma vez que está a fornecer recursos de hardware na nuvem e, portanto, é um recurso precioso que precisa de ser protegido. Por isso, utilizamos um processo HSMrequest@microsoft.comde whitelisting via e-mail usando . 

### <a name="getting-access-to-dedicated-hsm"></a>Obtenção de acesso a HSM dedicado

Se acredita que o HSM dedicado irá adequar-se aos seus principais requisitos de armazenamento, envie um e-mail HSMrequest@microsoft.com para solicitar acesso. Delineie a sua aplicação, as regiões que gostaria de HSMs e o volume de HSMs que procura. Se trabalhar com um representante da Microsoft, como um Executivo de Conta ou cloud solution architect, por exemplo, inclua-os em qualquer pedido.

## <a name="hsm-provisioning"></a>Prestação hSM

O fornecimento de um dispositivo HSM em Azure pode ser feito através do CLI ou da PowerShell. Ao registar-se para o serviço, será fornecido um modelo ARM de amostra e será dada assistência para a personalização inicial. 

### <a name="hsm-deployment-failure-information"></a>Informação sobre falhas de implementação do HSM

O HSM dedicado suporta clI e PowerShell para implementação, pelo que a informação de erro baseada no portal é limitada e não verbosa. Uma melhor informação pode ser encontrada utilizando o Explorador de Recursos. A página inicial do portal tem um ícone para esta e informação de erro mais detalhada está disponível. Esta informação ajuda muito se colada ao criar um pedido de apoio relacionado com questões de implementação.

![Informação de Falha](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>Delegação de subredes do HSM
A principal razão para as falhas de implantação é esquecer-se de definir a delegação adequada para a sub-rede definida pelo cliente em que os HSMs serão provisionados. A definição dessa delegação faz parte dos pré-requisitos da VNet e da subnet para a implantação e mais detalhes podem ser encontrados nos tutoriais.

![Delegação subnet](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Condição de corrida de implantação hsm

O modelo ARM padrão fornecido para a implantação tem recursos relacionados com o HSM e expressRoute gateway. Os recursos de networking são uma dependência para a implantação e o tempo de sucesso do HSM pode ser crucial.  Ocasionalmente, temos visto falhas de implantação relacionadas com problemas de dependência e reexecutar a implantação muitas vezes resolve o problema. Caso contrário, a desmarcação de recursos e, em seguida, a reafectação é muitas vezes bem sucedida. Depois de tentar este problema e ainda encontrar problema, levante um pedido de apoio no portal Azure selecionando o tipo de problema de "Questões que configuram a configuração do Azure".

### <a name="hsm-deployment-using-terraform"></a>Implantação de HSM usando terrafora

Alguns clientes usaram a Terraform como um ambiente de automação em vez de modelos ARM fornecidos ao registarem-se para este serviço. Os HSMs não podem ser implantados desta forma, mas os recursos de rede dependentes podem. Terraform tem um módulo para chamar para um modelo ARM mínimo que jut tem a implementação HSM.  Nesta situação, deve ser tomado o cuidado de garantir que os recursos de rede, como o Gateway ExpressRoute necessário, sejam totalmente implementados antes de implementar hSMs. O comando CLI seguinte pode ser utilizado para testar a implementação concluída e integrada conforme necessário. Substitua os suportes do suporte de ângulo para o seu nome específico. Você deve procurar o resultado de "provisioningState is Succeeded"

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Falha de implantação com base em quota
As implementações podem falhar se exceder 2 HSMs por carimbo e 4 HSMs por região. Para evitar esta situação, certifique-se de que eliminou os recursos de implementações anteriormente falhadas antes de voltar a ser implementado. Consulte o item "Como vejo hSMs" abaixo para verificar os recursos. Se acredita que precisa de exceder esta quota, que está principalmente lá como salvaguarda, por favor envie um e-mail HSMrequest@microsoft.com com detalhes.

### <a name="deployment-failure-based-on-capacity"></a>Falha de implantação com base na capacidade
Quando um determinado carimbo ou região está a ficar cheio, isto é, quase todos os HSM sgratuitos são provisionados, isso pode levar a falhas de implantação. Cada carimbo tem 11 HSMs disponíveis para os clientes, o que significa 22 por região. Há também 3 peças sobressalentes e 1 dispositivo de ensaio em cada carimbo. Se acredita que pode ter atingido HSMrequest@microsoft.com um limite, envie um e-mail para obter informações sobre o nível de preenchimento de selos específicos.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Como vejo hSMs quando aprovisionado?
Devido ao facto de o HSM dedicado ser um serviço de lista branca, é considerado um "Tipo Oculto" no portal Azure. Para ver os recursos HSM, deve verificar a caixa de verificação "Mostrar tipos escondidos", como mostrado abaixo. O recurso NIC segue sempre o HSM e é um bom local para descobrir o endereço IP do HSM antes de utilizar o SSH para ligar.

![Delegação subnet](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Recursos de Networking

A implantação de HSM dedicado tem uma dependência de recursos de networking e algumas limitações consequentes a ter em conta.

### <a name="provisioning-expressroute"></a>Provisionamento Rota Expresso

O HSM dedicado usa o ExpressRoute Gateway como um "túnel" para a comunicação entre o espaço de endereçoIP privado do cliente e o HSM físico num datacenter Azure.  Tendo em conta que existe uma restrição de um portal por Vnet, os clientes que necessitem de ligação aos seus recursos no local via ExpressRoute, terão de utilizar outro Vnet para essa ligação.  

### <a name="hsm-private-ip-address"></a>Endereço IP privado hSM

Os modelos de amostra fornecidos para o HSM dedicado assumem que o IP HSM será automaticamente retirado de uma determinada gama de subnet. Pode especificar um endereço IP explícito para o HSM através de um atributo "NetworkInterfaces" no modelo ARM. 

![Delegação subnet](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>Inicialização do HSM

A inicialização prepara um novo HSM para utilização, ou um HSM existente para reutilização. A inicialização do HSM deve estar completa antes de poder gerar ou armazenar objetos, permitir que os clientes se conectem ou realizem operações criptográficas.

### <a name="lost-credentials"></a>Credenciais Perdidas

A perda da palavra-passe do administrador da Shell resultará na perda de material chave HSM. Deve ser feito um pedido de apoio para repor o HSM.
Ao inicializar o HSM, guarde as credenciais de segurança. As credenciais Shell e HSM devem ser mantidas de acordo com as políticas da sua empresa.

### <a name="failed-logins"></a>Logins falhados

Fornecer credenciais incorretas aos HSMs pode ter consequências destrutivas. Seguem-se comportamentos predefinidos para funções HSM.

| Função | Limiar (# de tentativas) | Resultado de muitas más tentativas de login | Recuperação |
|--|--|--|--|
| HSM Assim | 3 |  O HSM está zeroizado (todas as identidades dos objetos HSM, e todas as divisórias desapareceram)  |  O HSM deve ser reinicializado. O conteúdo pode ser restaurado a partir de backups. | 
| Partição Assim | 10 |  A partição está zeroizada. |  A partição deve ser reinicializada. O conteúdo pode ser restaurado a partir de cópia de segurança. |  
| Auditoria | 10 | Bloqueio | Desbloqueado automaticamente após 10 minutos. |  
| Oficial de Cripto | 10 (pode ser diminuído) | Se a política hsm 15: Ativar o reset do PIN de partição está definido para 1 (ativado), as funções CO e CU estão bloqueadas.<br>Se a política do HSM 15: Ativar o reset do PIN de partição está definido para 0 (desativado), as funções CO e CU estão permanentemente bloqueadas e os conteúdos de partição já não estão acessíveis. Esta é a predefinição. | A função CO deve ser desbloqueada e a credencial redefinida pela Partição SO, utilizando `role resetpw -name co`.<br>A divisória deve ser reinicializada e o material chave restaurado a partir de um dispositivo de reserva. |  

## <a name="hsm-configuration"></a>Configuração HSM 

Os seguintes itens são situações em que os erros de configuração são comuns ou têm um impacto digno de chamar a atenção:

### <a name="hsm-documentation-and-software"></a>Documentação e Software HSM
O software e documentação dos dispositivos Thales SafeNet Luna 7 HSM não estão disponíveis na Microsoft e devem ser descarregados diretamente do Thales. A inscrição é necessária utilizando o ID do Cliente thales recebido durante o processo de registo. Os dispositivos, tal como fornecidos pela Microsoft, têm a versão 7.2 do software e a versão 7.0.3 do firmware. No início de 2020, o Thales tornou pública a documentação e pode ser encontrada [aqui.](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm)  

### <a name="hsm-networking-configuration"></a>Configuração de rede HSM

Tenha cuidado ao configurar a rede dentro do HSM.  O HSM tem uma ligação através do Gateway ExpressRoute de um espaço de endereço IP privado do cliente diretamente para o HSM.  Este canal de comunicação é apenas para comunicação com o cliente e a Microsoft não tem acesso. Se o HSM estiver configurado de tal forma que esta trajetória de rede seja impactada, isso significa que toda a comunicação com o HSM é removida.  Nesta situação, a única opção é levantar um pedido de suporte da Microsoft através do portal Azure para que o dispositivo seja reposto. Este procedimento de reset define o HSM de volta ao seu estado inicial e todo o material de configuração e chave é perdido.  A configuração deve ser recriada e quando o dispositivo se juntar ao grupo HA, obterá material chave replicado.  

### <a name="hsm-device-reboot"></a>Reinicialização do dispositivo HSM

Algumas alterações de configuração requerem que o HSM seja ciclo de alimentação ou reiniciado. O teste da Microsoft do HSM em Azure determinou que em algumas ocasiões o reboot poderia ser enforcado. A implicação é que um pedido de apoio deve ser criado no portal Azure solicitando um reinício duro e que pode demorar até 48 horas para concluir considerando que é um processo manual num datacenter Azure.  Para evitar esta situação, certifique-se de que implementou o patch de reinício disponível diretamente a partir de Thales. Consulte o [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) no Thales Luna Network HSM 7.2 Downloads para um patch recomendado para um problema de reinicialização (Nota: você precisará ter se registrado no portal de suporte Thales para descarregar).

### <a name="ntls-certificates-out-of-sync"></a>Certificados NTLS fora de sincronização
Um cliente pode perder a conectividade com um HSM quando um certificado expirar ou tiver sido substituído através de atualizações de configuração. A configuração do cliente de troca de certificados deve ser reaplicada a cada HSM.
Exemplo de registo ntls com certificado inválido:

> NTLS[8508]: informação : 0 : Pedido de ligação de entrada... : 192.168.50.2/59415 NTLS[8508]: Mensagem de erro da SSLAccept é: erro:14094418:Rotinas SSL:ssl3_read_bytes:tlsv1 alerta desconhecido ca NTLS[8508]: Erro durante sLL RC_SSL_ERROR ) NTLS[8508]: informação : 0xc0000711 : Não estabelecer um canal seguro com o cliente : 192.168.50.2/59415 : RC_SSL_FAILED_HANDSHAKE NTLS[8508]: informação : 0 : NTLS Cliente "Nome de anfitrião desconhecido" Instância de ligação removida : 192.168.50.2/594155

### <a name="failed-tcp-communication"></a>Comunicação TCP falhada

A comunicação da instalação do Cliente Luna ao HSM requer uma porta TCP mínima 1792. Considere isto como quaisquer configurações de rede são alteradas no ambiente.

### <a name="failed-ha-group-member-doesnt-recover"></a>Membro fracassado do grupo HA não recupera

Se um membro fracassado do Grupo HA não recuperar, deve ser recuperado manualmente do cliente Luna usando a recuperação do hagroup de comando.
É necessário configurar uma contagem de retry para um grupo HA para permitir a recuperação automática. Por defeito, um grupo HA não tentará recuperar um membro ha no grupo quando recuperar.

### <a name="ha-group-doesnt-sync"></a>Grupo HA não sincroniza

No caso de as divisórias dos membros não terem o mesmo domínio de clonagem, o comando de sincronização ha apresentará o seguinte: Aviso: O sincronizacro pode falhar.  Os membros da ranhura 0 e da ranhura 1 têm configurações contraditórias para a clonagem de chaves privadas.
Uma nova partição com o domínio de clonagem correto deve ser adicionada ao grupo HA, seguida da remoção da partição incorretamente configurada.

## <a name="hsm-deprovisioning"></a>Disprovisionamento HSM 

Só quando totalmente terminado com um HSM pode ser desprovisionado e, em seguida, a Microsoft irá redefini-lo e devolvê-lo a uma piscina gratuita. 

### <a name="how-to-delete-an-hsm-resource"></a>Como eliminar um recurso HSM

O recurso Azure para um HSM não pode ser eliminado a menos que o HSM esteja num estado "zeroized".  Por conseguinte, todo o material-chave deve ter sido apagado antes de tentar eliminá-lo como recurso. A maneira mais rápida de zeroizar é obter a senha de administração HSM errada 3 vezes (nota: isto refere-se à administração HSM e não ao nível do aparelho). A concha luna `hsm -factoryreset` tem um comando que zeroiza mas só pode ser executado através de consola na porta de série e os clientes não têm acesso a isso.

## <a name="next-steps"></a>Passos seguintes

Este artigo forneceu informações sobre áreas em todo o ciclo de vida de implantação do HSM que podem ter problemas ou exigir resolução de problemas ou cuidadosa consideração. Esperemos que este artigo o ajude a evitar atrasos e frustrações desnecessários, e se tiver adições ou alterações relevantes, então levante um pedido de suporte com a Microsoft e avise-nos. 
