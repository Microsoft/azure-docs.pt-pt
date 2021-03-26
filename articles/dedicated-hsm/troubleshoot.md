---
title: Resolução de problemas Dedicado HSM - Azure Dedicado HSM | Microsoft Docs
description: Visão geral do Azure Dedicado HSM fornece capacidades de armazenamento chave dentro do Azure que cumpre a certificação FIPS 140-2 Nível 3
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 11118c9bd745480dc88380e718a9ab348ab1a3e3
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606951"
---
# <a name="troubleshooting-the-azure-dedicated-hsm-service"></a>Resolução de problemas do serviço Azure Dedicado HSM

O serviço Azure Dedicado HSM tem duas facetas distintas. Em primeiro lugar, o registo e a implantação em Azure dos dispositivos HSM com os seus componentes de rede subjacentes. Em segundo lugar, a configuração dos dispositivos HSM em preparação para a utilização/integração com uma determinada carga de trabalho ou aplicação. Embora os dispositivos [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) sejam os mesmos em Azure como compraria diretamente ao Thales, o facto de serem um recurso em Azure cria algumas considerações únicas. Estas considerações e quaisquer insights ou boas práticas resultantes da resolução de problemas são documentados aqui para garantir uma elevada visibilidade e acesso a informações críticas. Uma vez que o serviço está em uso, informações definitivas estão disponíveis através de pedidos de suporte diretamente para Microsoft ou Thales. 

> [!NOTE]
> Note-se que antes de efetuar qualquer configuração num dispositivo HSM recém-implantado, este deve ser atualizado com quaisquer patches relevantes. Um patch específico exigido é [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) no portal de suporte Thales que aborda um problema em que o sistema fica sem resposta durante o reboot.

## <a name="hsm-registration"></a>Registo HSM

O HSM dedicado não está livremente disponível para uso, uma vez que está a fornecer recursos de hardware na nuvem e, portanto, é um recurso precioso que precisa de ser protegido. Por isso, utilizamos um processo de licenciamento através de e-mail utilizando HSMrequest@microsoft.com . 

### <a name="getting-access-to-dedicated-hsm"></a>Ter acesso ao HSM dedicado

Primeiro pergunte-se quais os casos de uso que você tem que não podem ser abordados por [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/general/overview) ou [Azure Managed HSM](https://docs.microsoft.com/en-us/azure/key-vault/managed-hsm/overview). Se então acredita que apenas o HSM dedicado irá adequar-se aos seus principais requisitos de armazenamento, então envie um e-mail HSMrequest@microsoft.com para solicitar acesso. Delineie a sua aplicação e use os casos, as regiões que gostaria de HSMs e o volume de HSMs que procura. Se trabalhar com um representante da Microsoft, como um Executivo de Contas ou Cloud Solution Architect, por exemplo, inclua-os em qualquer pedido.

## <a name="hsm-provisioning"></a>Provisão do HSM

O fornecimento de um dispositivo HSM em Azure pode ser feito através de CLI ou PowerShell. Ao registar-se para o serviço, será fornecido um modelo ARM de amostra e será prestada assistência para a personalização inicial. 

### <a name="hsm-deployment-failure-information"></a>Informações de falha de implantação do HSM

O HSM dedicado suporta o CLI e o PowerShell para a implementação, pelo que as informações de erro baseadas no portal são limitadas e não verbosas. Uma melhor informação pode ser encontrada utilizando o Explorador de Recursos. A página inicial do portal tem um ícone para isso e informações de erro mais detalhadas estão disponíveis. Esta informação ajuda muito se colada ao criar um pedido de apoio relacionado com questões de implantação.

![Informação sobre Falhas](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>Delegação de Sub-rede HSM
A principal razão para as falhas de implantação é esquecer-se de definir a delegação adequada para a sub-rede definida pelo cliente em que os HSMs serão a provisionados. A definição dessa delegação faz parte dos pré-requisitos do VNet e da sub-rede para a implantação e mais detalhes podem ser encontrados nos tutoriais.

![Delegação sub-rede](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Condição de corrida de implantação do HSM

O modelo padrão ARM previsto para a implantação tem recursos relacionados com o gateway HSM e ExpressRoute. Os recursos de rede são uma dependência para uma implantação bem sucedida do HSM e o tempo pode ser crucial.  Ocasionalmente, temos visto falhas de implantação relacionadas com problemas de dependência e a repetição da implementação muitas vezes resolve o problema. Caso contrário, a eliminação de recursos e, em seguida, a redistribuição é muitas vezes bem sucedida. Depois de tentar e ainda encontrar problema, levante um pedido de apoio no portal Azure selecionando o tipo de problema de "Problemas que configuram a configuração do Azure".

### <a name="hsm-deployment-using-terraform"></a>Implantação do HSM usando terraforme

Alguns clientes utilizaram o Terraform como ambiente de automatização em vez de modelos ARM como fornecidos ao registarem-se para este serviço. Os HSMs não podem ser implantados desta forma, mas os recursos de rede dependentes podem. Terraform tem um módulo para chamar para um modelo ARM mínimo que apenas tem a implementação HSM.  Nesta situação, deve-se ter cuidado para garantir que os recursos de networking, tais como o Gateway ExpressRoute necessário, sejam totalmente implantados antes da implantação dos HSMs. O seguinte comando CLI pode ser utilizado para testar a implementação completa e integrado conforme necessário. Substitua os suportes de suporte de ângulo para o seu nome específico. Você deve procurar um resultado de "ProvisioningState is Succeeded"

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Falha de implantação com base na quota
As implementações podem falhar se exceder 2 HSMs por selo e 4 HSMs por região. Para evitar esta situação, certifique-se de que eliminou recursos de implementações anteriormente falhadas antes de voltar a ser implementado. Consulte o item "Como vejo HSMs" abaixo para verificar recursos. Se você acredita que precisa de exceder esta quota, que está principalmente lá como uma salvaguarda, então por favor envie um e-mail HSMrequest@microsoft.com com detalhes.

### <a name="deployment-failure-based-on-capacity"></a>Falha de implantação baseada na capacidade
Quando um determinado carimbo ou região está a ficar cheio, ou seja, quase todos os HSMs livres são a provisionados, isso pode levar a falhas de implantação. Cada carimbo tem 12 HSMs disponíveis para os clientes, o que significa 24 por região. Há também 2 peças sobressalentes e 1 dispositivo de teste em cada carimbo. Se acredita que pode ter atingido um limite, envie um e-mail HSMrequest@microsoft.com para obter informações sobre o nível de preenchimento de selos específicos.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Como vejo os HSMs quando a provisionados?
Devido ao HSM dedicado ser um serviço de lista suspensa, é considerado um "Tipo Escondido" no portal Azure. Para ver os recursos do HSM, deve verificar a caixa de verificação "Mostrar tipos ocultos", como mostrado abaixo. O recurso NIC segue sempre o HSM e é um bom local para descobrir o endereço IP do HSM antes de utilizar o SSH para se ligar.

![Screenshot que destaca o show de tipos escondidos verificar](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Recursos de Networking

A implantação de HSM dedicado tem uma dependência de recursos de networking e algumas limitações consequentes a ter em conta.

### <a name="provisioning-expressroute"></a>Provisionamento ExpressRoute

O HSM dedicado utiliza o ExpressRoute Gateway como um "túnel" para a comunicação entre o espaço de endereço IP privado do cliente e o HSM físico num datacenter Azure.  Tendo em conta que existe uma restrição de um gateway por Vnet, os clientes que necessitem de ligação aos seus recursos no local via ExpressRoute, terão de utilizar outro Vnet para essa ligação.  

### <a name="hsm-private-ip-address"></a>Endereço IP privado HSM

Os modelos de amostra fornecidos para o HSM dedicado assumem que o IP HSM será automaticamente retirado de uma determinada gama de sub-redes. Pode especificar um endereço IP explícito para o HSM através de um atributo "NetworkInterfaces" no modelo ARM. 

![Screenshot que mostra o modelo de amostra para HSM dedicado.](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>Inicialização do HSM

A inicialização prepara um novo HSM para utilização, ou um HSM existente para reutilização. A inicialização do HSM tem de estar completa antes de poder gerar ou armazenar objetos, permitir que os clientes se conectem ou realizem operações criptográficas.

### <a name="lost-credentials"></a>Credenciais Perdidas

A perda da palavra-passe do administrador da Shell resultará na perda de material-chave HSM. Deve ser feito um pedido de apoio para repor o HSM.
Ao rubricar o HSM, armazenar de forma segura as credenciais. As credenciais da Shell e do HSM devem ser mantidas de acordo com as políticas da sua empresa.

### <a name="failed-logins"></a>Logins falhados

Fornecer credenciais incorretas aos HSMs pode ter consequências destrutivas. Seguem-se comportamentos predefinidos para funções HSM.

| Função | Limiar (# de tentativas) | Resultado de muitas tentativas de login | Recuperação |
|--|--|--|--|
| HSM SO | 3 |  O HSM é zeroizado (todas as identidades de objetos HSM, e todas as divisórias desapareceram)  |  O HSM tem de ser reinicializado. O conteúdo pode ser restaurado a partir de cópias de segurança. | 
| Partição SO | 10 |  A partição está zeroizada. |  A partição tem de ser reinicializada. O conteúdo pode ser restaurado a partir de cópia de segurança. |  
| Auditoria | 10 | Lockout | Desbloqueado automaticamente após 10 minutos. |  
| Crypto Officer | 10 (pode ser diminuído) | Se a política de HSM 15: Ativar o reset SO do PIN de partição definido para 1 (ativado), as funções de CO e CU estão bloqueadas.<br>Se a política de HSM 15: Ativar o reset SO do PIN de partição definido para 0 (desativado), as funções de CO e CU estão permanentemente bloqueadas e o conteúdo da partição já não está acessível. Esta é a predefinição. | A função co deve ser desbloqueada e a credencial reposta pela Partição SO, utilizando `role resetpw -name co` .<br>A partição deve ser reinicializada e o material chave restaurado a partir de um dispositivo de reserva. |  

## <a name="hsm-configuration"></a>Configuração HSM 

Os seguintes itens são a situação em que os erros de configuração são comuns ou têm um impacto digno de chamar:

### <a name="hsm-documentation-and-software"></a>Documentação e Software HSM
O software e documentação para os dispositivos [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) não está disponível da Microsoft e deve ser descarregado diretamente do Thales. É necessária a inscrição utilizando o ID do Cliente Thales recebido durante o processo de registo. Os dispositivos, tal como fornecidos pela Microsoft, têm a versão 7.2 do software e a versão 7.0.3 do firmware. No início de 2020 Thales tornou a documentação pública e pode ser encontrada [aqui.](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm)  

### <a name="hsm-networking-configuration"></a>Configuração de rede HSM

Tenha cuidado ao configurar a ligação em rede dentro do HSM.  O HSM tem uma ligação através do Portal ExpressRoute de um espaço de endereço IP privado do cliente diretamente para o HSM.  Este canal de comunicação destina-se apenas à comunicação do cliente e a Microsoft não tem acesso. Se o HSM estiver configurado de tal forma que este caminho de rede seja impactado, isso significa que toda a comunicação com o HSM é removida.  Nesta situação, a única opção é levantar um pedido de suporte da Microsoft através do portal Azure para que o dispositivo seja reiniciado. Este procedimento de reset define o HSM de volta ao seu estado inicial e toda a configuração e material chave é perdido.  A configuração deve ser recriada e quando o dispositivo se junta ao grupo HA, será replicado o material-chave.  

### <a name="hsm-device-reboot"></a>Reinicialização do dispositivo HSM

Algumas alterações de configuração requerem que o HSM seja ciclo de potência ou reiniciado. Os testes da Microsoft do HSM em Azure determinaram que, em algumas ocasiões, o reboot poderia parar de responder. A implicação é que um pedido de apoio deve ser criado no portal Azure solicitando o hard-reboot e que pode levar até 48 horas para ser concluído considerando que é um processo manual num datacenter Azure.  Para evitar esta situação, certifique-se de que implementou o patch de reinicialização disponível diretamente no Thales. Consulte [o KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) no Thales Luna 7 HSM 7.2 Descarregue para um patch recomendado para um problema em que o sistema não responde durante o reboot (Nota: terá de se registar no portal de suporte ao [cliente Thales](https://supportportal.thalesgroup.com/csm) para descarregar).

### <a name="ntls-certificates-out-of-sync"></a>Certificados NTLS fora de sincronização
Um cliente pode perder a conectividade com um HSM quando um certificado expira ou foi substituído através de atualizações de configuração. A configuração do cliente de troca de certificados deve ser reaplicada com cada HSM.
Registo de NTLS com certificado inválido:

> NTLS[8508]: informação: 0 : Pedido de ligação de entrada... 192.168.50.2/59415 NTLS[8508]: Mensagem de erro de SSLAccept é: erro:14094418:Rotinas SSL:ssl3_read_bytes:tlsv1 alerta desconhecido ca NTLS[8508]: Erro durante a aceitação SSL ( RC_SSL_ERROR ) NTLS[8508]: info: 0xc0000711 : Não conseguir estabelecer um canal seguro com o cliente: 192.168.50.2/59415 : RC_SSL_FAILED_HANDSHAKE 0xc0000711 instância de ligação removida: 192.168.50.2/59415 do cliente NTLS

### <a name="failed-tcp-communication"></a>Falha na comunicação da TCP

A comunicação da instalação do Cliente Luna ao HSM requer uma porta TCP mínima 1792. Considere isto como se quaisquer configurações de rede forem alteradas no ambiente.

### <a name="failed-ha-group-member-doesnt-recover"></a>Membro falhado do Grupo HA não recupera

Se um membro falhado do Grupo HA não recuperar, deve ser recuperado manualmente do cliente Luna usando a recuperação do comando hagroup.
É necessário configurar uma contagem de repetição para um grupo HA para permitir a recuperação automática. Por defeito, um grupo HA não tentará recuperar um membro ha no grupo quando recuperar.

### <a name="ha-group-doesnt-sync"></a>O Grupo HA não sincroniza

No caso de as divisórias dos membros não terem o mesmo domínio de clonagem, o comando ha synchronize apresentará o seguinte: Aviso: A sincronização pode falhar.  Os membros da ranhura 0 e da ranhura 1 têm configurações conflituosas para a clonagem de chaves privadas.
Deve ser adicionada uma nova divisória com o domínio correto da clonagem ao grupo HA, seguida da remoção da partição incorretamente configurada.

## <a name="hsm-deprovisioning"></a>Desprovisionamento do HSM 

Só quando estiver completamente concluído com um HSM poderá ser desprovisionado e, em seguida, a Microsoft reiniciá-lo-á e devolver-o-á a uma piscina gratuita. 

### <a name="how-to-delete-an-hsm-resource"></a>Como eliminar um recurso HSM

O recurso Azure para um HSM não pode ser eliminado a menos que o HSM esteja num estado "zeroizado".  Por conseguinte, todo o material-chave deve ter sido eliminado antes de tentar eliminá-lo como recurso. A forma mais rápida de zeroizar é obter a palavra-passe de administração HSM errada 3 vezes (nota: isto refere-se à administração hsm e não ao administrador de nível de aparelho). A concha Luna tem um `hsm -factoryreset` comando que zeroiza mas só pode ser executado através da consola na porta em série e os clientes não têm acesso a isso.

## <a name="next-steps"></a>Passos seguintes

Este artigo forneceu informações sobre áreas em todo o ciclo de vida de implantação do HSM que podem ter problemas ou exigir resolução de problemas ou consideração cuidadosa. Esperemos que este artigo o ajude a evitar atrasos e frustrações desnecessários, e se tiver adições ou alterações relevantes, em seguida, levante um pedido de suporte com a Microsoft e avise-nos. 
