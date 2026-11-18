# Tomada Vital — IoT | Global Solution FIAP 2026/2

**Disciplina:** Disruptive Architectures: IoT, IoB & Generative AI  
**Turma:** 2TDSPR  
**Tema da Global Solution:** CitizenTech

## Simulação do circuito no Tinkercad

**[Abrir circuito compartilhado da Tomada Vital no Tinkercad](https://www.tinkercad.com/things/g958jGWu1qs-fantabulous-bruticus-kup/editel?returnTo=%2Fthings%2Fg958jGWu1qs-fantabulous-bruticus-kup&sharecode=o65KUUcEojXKD6Cn6BC4HaEw2s_Gv3T1Dquyp1OA2sM)**

O circuito contém a montagem Arduino UNO com os componentes e o código usados nos testes da Global Solution. Para reproduzir os cenários, utilize o interruptor deslizante, o potenciômetro e o Monitor Serial, seguindo as instruções deste README e os [registros dos testes](evidencias/README.md).

## Proposta

A Tomada Vital é uma proposta de monitoramento de quedas de energia em equipamentos médicos que dependem de alimentação elétrica. O protótipo demonstra, com um **Arduino UNO simulado no Tinkercad**, como identificar uma queda, estimar a autonomia de uma bateria e sinalizar a prioridade do evento.

O cenário didático usa o dispositivo **TV-001**, um concentrador de oxigênio associado à paciente fictícia Maria Aparecida. A autonomia de 480 minutos e os limites de prioridade são parâmetros **simulados para fins acadêmicos**, não recomendações clínicas.

## Equipe

| Integrante | RM |
|---|---|
| Diego Paulino | 566841 |
| Guilherme Alves Dabul | 559901 |
| Renan Lima | 568321 |
| Ryan Mazali | 567168 |
| Thiago Alexandre | 567486 |

## Protótipo Arduino

Arquivo de código: [`tomada_vital.ino`](tomada_vital.ino).

| Componente | Pino | Função |
|---|---|---|
| Interruptor deslizante | D2 | Simula presença de energia (5V) ou queda (GND) |
| Potenciômetro | A0 | Simula bateria entre 0% e 100% |
| LED verde | D9 | Energia normal |
| LED amarelo | D10 | Queda com prioridade ALTA ou MODERADA |
| LED vermelho | D11 | Queda com prioridade CRITICA |
| Buzzer piezoelétrico | D8 | Alerta sonoro na prioridade CRITICA |

**Montagem:** Arduino UNO R3, protoboard, três LEDs (cada um em série com um resistor de 220 Ω), chave de três terminais, potenciômetro e buzzer. Conectar os negativos dos LEDs e do buzzer ao GND comum. No interruptor, conectar um terminal externo a 5V, o central a D2 e o outro externo ao GND. No potenciômetro, os extremos vão a 5V e GND e o terminal central a A0. O Arduino distribui 5V e GND pelos barramentos da protoboard.

## Funcionamento

Quando a energia está presente, o LED verde fica aceso. Na queda, a bateria simulada é lida pela entrada A0 e o Arduino calcula:

```text
minutos_restantes = max(0, 480 * bateria_na_queda / 100 - minutos_desde_a_queda)
```

A bateria é lida **uma única vez, no instante da queda**. Depois disso, o consumo é descontado pelo tempo que passou. É a mesma regra da API Java da solução, que recebe a bateria do momento do alerta. Reler a bateria a cada segundo e ainda descontar o tempo contaria o gasto duas vezes. Por isso, mexer no potenciômetro durante uma queda não altera o cálculo: para testar outra bateria, restabeleça a energia, ajuste o potenciômetro e simule uma nova queda.

Para acelerar os testes, **1 segundo da simulação representa 1 minuto**.

| Autonomia restante | Prioridade | Indicação |
|---|---|---|
| Até 120 min | CRITICA | LED vermelho e buzzer |
| De 121 a 360 min | ALTA | LED amarelo |
| Acima de 360 min | MODERADA | LED amarelo |

Os nomes de prioridade não usam acentos no código porque seguem o padrão de integração combinado entre as disciplinas.

## Como executar

1. Abrir [Tinkercad Circuits](https://www.tinkercad.com/circuits).
2. Criar ou abrir um circuito com Arduino UNO e os componentes descritos acima.
3. Conectar os componentes seguindo a tabela de pinos.
4. Abrir **Código → Texto** e substituir o código de exemplo pelo conteúdo completo de [`tomada_vital.ino`](tomada_vital.ino).
5. Iniciar a simulação e abrir o **Monitor Serial** a **9600 baud**.
6. Alternar o interruptor e o potenciômetro para testar os cenários abaixo.

## Testes funcionais — simulação Arduino 2.0

| Cenário | Ação | Comportamento esperado | Resultado observado em 18/11/2026 |
|---|---|---|---|
| 1. Energia normal | Chave em 5V | LED verde e mensagem inicial | LED verde; `TV-001;INICIO;monitorando a energia` |
| 2. Queda com 100% | Chave em GND com bateria em 100% | LED amarelo, MODERADA | `bateria=100;restante=480;prioridade=MODERADA` |
| 3. Queda com 75% | Restabelecer, ajustar bateria e provocar nova queda | LED amarelo, ALTA | `bateria=75;restante=360;prioridade=ALTA` |
| 4. Queda com 13% | Restabelecer, ajustar bateria e provocar nova queda | LED vermelho e buzzer, CRITICA | `bateria=13;restante=62;prioridade=CRITICA`; buzzer indicado como ativo |
| 5. Restabelecimento | Voltar a chave para 5V | LED verde; buzzer desligado; mensagem de retorno | `TV-001;RESTABELECIDO` no histórico do Monitor Serial |

**Observações dos testes realizados no Tinkercad (18/11/2026):** documentamos os estados MODERADA, ALTA e CRITICA com o código atualizado. No histórico de cada ocorrência o valor de `bateria` permaneceu constante e o tempo restante diminuiu. O Monitor Serial também registrou o restabelecimento da energia. Os resultados são de simulação, não de testes em equipamentos médicos reais. Consulte as [Evidências da simulação](evidencias/README.md).

Trechos registrados no Monitor Serial:

```text
TV-001;QUEDA;bateria=100;restante=480;prioridade=MODERADA
TV-001;QUEDA;bateria=75;restante=360;prioridade=ALTA
TV-001;QUEDA;bateria=13;restante=62;prioridade=CRITICA
TV-001;RESTABELECIDO
```

Para reiniciar o tempo da ocorrência, volte a chave ao estado de energia normal antes de simular outra queda.

## Diagrama visual de arquitetura

O diagrama separa o protótipo efetivamente testado no Tinkercad da comunicação celular/MQTT proposta. **[Abrir diagrama de arquitetura e rede](docs/arquitetura.md)**.

## Arquitetura de comunicação proposta

**Importante:** esta seção descreve uma **proposta**. O Tinkercad não tem módulo celular nem acesso à internet; no protótipo, cada evento aparece no Monitor Serial.

```text
Sensores simulados (chave + potenciômetro)
                  |
       Arduino UNO (TV-001)  <-- bateria própria do dispositivo
                  |
     Saída Serial (implementada no protótipo)
                  |
   Módulo celular NB-IoT ou LTE-M (proposto)
                  |
  Rede da operadora (independe do Wi-Fi da casa)
                  |
     Broker MQTT com TLS (porta 8883)
       tópico tomadavital/TV-001/eventos
                  |
  API Java (assina o tópico e registra a ocorrência)
                  |
     Fila do operador e app da família

  Plano B: SMS quando a rede de dados falhar
```

### Por que cada escolha

- **Rede celular, e não Wi-Fi:** quando a luz cai, o roteador e o modem da casa também desligam. Um alerta que dependesse do Wi-Fi falharia justamente quando é necessário.
- **Bateria própria no dispositivo:** o dispositivo precisa continuar ligado durante o apagão para conseguir medir e avisar.
- **NB-IoT ou LTE-M:** são tecnologias celulares feitas para IoT. Consomem pouca energia (o que prolonga a bateria do dispositivo), têm boa cobertura dentro de casas e bastam para mensagens pequenas como a do alerta. A escolha entre as duas depende da cobertura da operadora no endereço.
- **MQTT:** protocolo leve de publicação e assinatura, adequado a dispositivos com pouca energia e a conexões instáveis. Com QoS 1, o broker confirma o recebimento e o dispositivo reenvia a mensagem se a confirmação não chegar.
- **TLS na porta 8883:** o alerta revela que alguém na casa usa equipamento vital, um dado pessoal sensível de saúde pela LGPD. Por isso, a comunicação é criptografada.
- **SMS como plano B:** se a rede de dados estiver indisponível ou congestionada, o dispositivo envia um SMS curto, que usa um canal diferente da rede de dados.
- **Sinal de vida:** além dos alertas, o dispositivo publica periodicamente (por exemplo, a cada 15 minutos) uma mensagem de status. Se ela parar de chegar, a central sabe que o dispositivo está sem sinal ou sem bateria.

### Mensagem do alerta

No protótipo, cada evento aparece no Monitor Serial em texto, por exemplo:

```text
TV-001;QUEDA;bateria=20;restante=96;prioridade=CRITICA
```

Na versão proposta, o dispositivo publicaria no tópico `tomadavital/TV-001/eventos` apenas os dados de que a API precisa — o código do dispositivo e a bateria no momento da queda —, no mesmo formato que a nossa API Java já recebe no endpoint `POST /api/ocorrencias`:

```json
{ "codigoDispositivo": "TV-001", "bateriaPercentual": 20 }
```

A API recalcula os minutos restantes e a prioridade com a mesma regra do Arduino e coloca a ocorrência na fila de atendimento. O código da API Java está em https://github.com/DiegoCPaulino/API-Tomada-Vital. A ligação entre o broker e a API é uma proposta e não foi implementada nesta entrega.

## Decisões técnicas

- **Arduino UNO:** plataforma usada nas atividades da disciplina e adequada à demonstração de entradas e saídas digitais e analógicas.
- **Chave deslizante:** representa estados estáveis de rede elétrica presente ou ausente.
- **Potenciômetro:** permite variar a bateria manualmente e testar os diferentes limiares.
- **LEDs e buzzer:** proporcionam retorno visual e sonoro ao estado monitorado.
- **Monitor Serial:** torna os eventos verificáveis no Tinkercad e representa a origem dos dados para a integração proposta.
- **Rede proposta:** as justificativas da rede celular, da bateria própria, do MQTT com TLS, do SMS e do sinal de vida estão em [Por que cada escolha](#por-que-cada-escolha).
- **Bateria lida só no início da queda:** mantém a mesma regra da API Java e evita descontar o consumo duas vezes.

## Limitações e cuidados

- Não foram implementados sensores reais de tensão de rede ou de nível de bateria; chave e potenciômetro são substitutos didáticos.
- O Tinkercad usado não realiza a comunicação celular/MQTT proposta; a saída disponível no protótipo é a Serial.
- O cálculo de autonomia é simplificado e usa parâmetros acadêmicos. Não deve ser empregado para determinar a segurança de pacientes ou de equipamentos de suporte à vida.
- Não foram medidos disponibilidade de rede, latência MQTT, consumo real de energia ou confiabilidade de alarmes em campo.
- O circuito e os testes estão documentados nas [evidências](evidencias/README.md); o [diagrama de arquitetura](docs/arquitetura.md) distingue o protótipo executado da rede apenas proposta.
- A leitura do percentual de bateria de um equipamento médico real depende do fabricante e pode não ser possível; no protótipo, o potenciômetro representa esse valor.

## Relação com CitizenTech

A proposta busca apoiar cidadãos que dependem de equipamentos médicos elétricos, usando monitoramento e informação de prioridade para fundamentar um fluxo de atendimento. A ideia se relaciona aos seguintes ODS:

- **ODS 3 (Saúde e Bem-Estar):** protege quem depende de equipamento médico elétrico em casa.
- **ODS 9 (Indústria, Inovação e Infraestrutura):** usa IoT e rede celular para dar informação em tempo real durante falhas de infraestrutura.
- **ODS 11 (Cidades e Comunidades Sustentáveis):** torna a resposta a apagões urbanos mais resiliente.
- **ODS 13 (Ação Contra a Mudança Global do Clima):** apoia a adaptação a eventos climáticos extremos, como os temporais que causaram os grandes apagões em São Paulo.

O impacto operacional ainda depende de validações em condições reais.

## Uso de IA generativa

Usamos ferramentas de IA generativa como apoio na organização e na revisão da documentação. O código, o circuito e os testes foram executados e conferidos por nós no Tinkercad.
