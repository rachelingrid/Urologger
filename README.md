# Urologger — Controle de Diurese Domiciliar

Aplicativo web para acompanhamento do débito urinário de pacientes em uso de coletor no domicílio. O cuidador fotografa o coletor a cada esvaziamento; o aplicativo confere a foto, lê a graduação, registra aspecto e queixas, e calcula o débito em mL/h e mL/kg/h.

O princípio é simples: cada esvaziamento é um ponto de medida. Quanto mais frequentes os registros, menor a janela de incerteza e mais fiel a estimativa de fluxo urinário.

## Por que existe

O débito urinário é um dos sinais mais precoces de deterioração clínica, e no ambiente hospitalar é medido de hora em hora. No domicílio, quase sempre se perde: o cuidador esvazia o coletor, olha, e a informação evapora. Sem registro estruturado, a família relata "urinou pouco hoje" e a equipe não tem como qualificar isso.

Este aplicativo transforma um gesto que já acontece — esvaziar o coletor — em dado clínico rastreável, sem acrescentar tarefa ao cuidador.

## Cadastros

**Paciente** — nome, data de nascimento, peso e altura. O peso é o que permite converter mL/h em mL/kg/h e aplicar o limiar de oligúria.

**Modelo de coletor** — cadastrado uma vez, com três medidas lidas no próprio coletor: **medida máxima**, **medida mínima** e **escala** (valor entre traços). Com as três, o aplicativo sabe quantos traços a graduação deve ter e quanto vale cada um; o cadastro é recusado se a diferença entre máxima e mínima não for múltipla da escala. O aplicativo nasce com quatro modelos padrão e escolhe sozinho o compatível com a capacidade do paciente.

## Aquisição da imagem

Antes de abrir a câmera, um diálogo apresenta as condições de captura: bolsa alinhada e sem dobras, flash em ambiente escuro, câmera perpendicular na altura do líquido, escala inteira no quadro. O cuidador pode dispensar o lembrete depois de habituado.

Durante a captura, o acelerômetro alimenta uma bolha de nível, e o aplicativo mede continuamente na pré-visualização. Quando quatro leituras seguidas concordam e o aparelho está aprumado, **dispara sozinho** e já preenche o volume. O cuidador aponta e espera.

## Crivo de qualidade

A foto é validada antes de qualquer medida. Desfoque, inclinação e escala cortada não são cenários de leitura — são motivos de recusa, e cada recusa informa ao cuidador o que corrigir.

1. **Contraste** — imagem escura demais é recusada antes de tudo.
2. **Nitidez** — variância do laplaciano normalizada; um desfoque de 2 pixels é suficiente para recusar.
3. **Inclinação** — ângulo real dos traços, medido pela tinta; corte em 4°.
4. **Escala cortada** — se o primeiro traço estiver colado na borda superior, recusa, mesmo que os números permitissem ler.

## Leitura da graduação

A tinta da graduação é localizada por **contraste local** em relação ao fundo, e não por cor. Isso torna a leitura independente do fabricante — verde, azul ou preta — e imune à urina escura: numa região grande e uniforme, o fundo é igualmente escuro e o contraste dá zero; só estruturas finas sobrevivem, que é exatamente o que a tinta é.

**Método principal — pilha de traços.** Os traços da escala são empilhados na mesma coluna, com espaçamento regular. Agrupar por alinhamento e exigir regularidade elimina intrusos (marcas de 25/50/75 mL, texto, dobras). O traço mais alto vale a medida máxima; o mais baixo vale a mínima, mas só quando a pilha está completa — enquanto faltar traço, não se sabe qual valor o mais baixo representa.

**Conferência — números impressos.** Os rótulos da escala são reconhecidos por casamento de gabarito e ajustados a uma reta por consenso, que descarta rótulos falsos. Serve de segunda medida independente, não de método principal.

**Verificação cruzada.** As medidas disponíveis precisam concordar dentro de 1,2 passo da escala. Se divergirem, a leitura é recusada — o sistema prefere não responder a devolver um número plausível e errado.

**Resolução.** O volume é arredondado ao passo da escala. Abaixo da menor marca impressa, registra-se "< mínima" e, para o cálculo de débito, usa-se a metade dela, sinalizado.

Quando a leitura automática é recusada, o cuidador pode marcar base, topo e nível por três toques na foto, ou digitar o volume. A procedência de cada medida (graduação, marcação ou digitação) e a confiança ficam gravadas no registro, no CSV e no relatório.

## Data e hora

Foto tirada no momento: instante do disparo. Foto escolhida da galeria: data original contida no arquivo (EXIF) e, na ausência dela, o padrão de data no nome do arquivo, que cobre os formatos usuais de câmera Android, Pixel e WhatsApp. A orientação EXIF é corrigida antes da análise — sem isso, fotos da galeria seriam analisadas na região errada. Sem nenhuma fonte de data, o campo é sinalizado para ajuste manual.

## Registro clínico

A cada esvaziamento: volume, aspecto em escala de sete tons (sugerido pela cor média da imagem e confirmado pelo cuidador), odor em seis categorias, queixas do paciente em lista numerada — ardência, dor suprapúbica, dor lombar, náusea, vômito, distensão abdominal, febre, confusão — e observações livres.

Sinalizações automáticas: hematúria macroscópica pelo aspecto; suspeita de infecção quando febre ou ardência coincidem com urina alterada; suspeita de sonda obstruída quando há distensão abdominal com coletor pouco cheio; oligúria abaixo de 0,5 mL/kg/h.

## Cálculo do débito

O primeiro registro do dia marca o início da janela e não entra na soma — o volume contido nele acumulou-se durante um período não medido. Os registros seguintes somam-se e dividem-se pelas horas decorridas desde aquele marco.

## Saídas

**Relatório em PDF** — período, identificação do paciente, indicadores de síntese, gráfico do débito diário em vetor, tabela por dia, contagem de queixas e a relação completa de registros com a cor de cada aspecto. Gerado pela impressão do navegador, com a opção *Salvar como PDF* do próprio aparelho.

**CSV** — todos os registros, com procedência e confiança de cada medida.

## Instalação

O aplicativo é publicado pelo GitHub Pages e não depende de servidor, banco de dados ou qualquer serviço externo.

1. Abra o endereço do Pages no navegador do celular
2. Android: menu do Chrome, *Instalar app*. iOS: compartilhar, *Adicionar à Tela de Início*
3. A partir daí abre em tela cheia e funciona sem conexão

Câmera e sensor de inclinação exigem HTTPS, disponível pelo Pages. Abrir o arquivo diretamente do armazenamento do aparelho desabilita essas funções.

## Privacidade

Nenhum dado sai do aparelho. Registros, fotos e dados do paciente ficam no armazenamento local do navegador, e não há qualquer transmissão para servidores. As exportações são manuais. Como consequência, desinstalar o aplicativo ou limpar os dados do navegador apaga o histórico — exporte periodicamente.

## Resultados de teste

Testes em bancada sobre fotografia real de coletor de sistema fechado de 2.000 mL (graduação de 100 mL) e variações controladas dessa imagem:

| Condição | Resultado |
|---|---|
| Escala enquadrada, boa luz | 900 mL, 87% — dois métodos concordantes (913 e 899) |
| Hematúria franca (simulada) | 900 mL, 90% — pilha reduzida de 13 para 11 traços |
| Urina cor de borra de vinho (simulada) | 900 mL, 90% |
| Desfoque de 2 px | recusa: foto desfocada |
| Rotação de 5° | recusa: coletor inclinado |
| Escala cortada no topo | recusa: escala cortada |
| Quadro inteiro, de longe | recusa: escala não formada |
| Brilho ÷ 4 | recusa: contraste insuficiente |
| Coletor de outro fabricante, tinta azul, imagem de baixa resolução | recusa: escala não formada |

Leitura visual de referência da foto: ~950 mL. Nenhum falso positivo.

## Limitações

**A leitura automática não foi validada em campo.** Um modelo de coletor testado, uma fotografia de origem, degradações simuladas. Isso demonstra viabilidade do princípio, não desempenho clínico. A formação da pilha e o reconhecimento de números em fotografias reais de outros fabricantes ainda não foram testados; a bolsa de tinta azul disponível era uma captura de web em baixa resolução e com perspectiva, e foi corretamente recusada.

**A âncora inferior raramente está disponível.** Com o coletor cheio, os traços de baixo ficam sob a urina e perdem contraste; com hematúria, isso é certo. A contagem de baixo só é liberada quando a pilha está completa, e na prática a leitura se apoia na âncora superior e nos números.

**Os valores de referência clínica são padrões gerais** e não foram validados para este aplicativo. Trata-se de ferramenta de registro e alerta para o cuidador, não de dispositivo diagnóstico, e não substitui a avaliação da equipe assistente.

## Próximo passo de produto

Um suporte mecânico que fixe bolsa e celular em geometria conhecida elimina alinhamento, distância, rotação e inclinação como variáveis, permite calibração única por modelo e habilita captura periódica automática — débito medido continuamente, com intervenção zero do cuidador. Está em fase de conceito.

## Estrutura

```
index.html      aplicativo completo — interface, crivo, visão computacional, armazenamento, relatório
sw.js           service worker, responsável pelo funcionamento offline
manifest.json   metadados de instalação na tela inicial
```

Ao alterar o `index.html`, incremente a versão do cache no `sw.js` (`diurese-v14` → `diurese-v15`). Sem isso, os aparelhos continuam servindo a versão antiga.

## Histórico de versões

| Cache | O que mudou |
|---|---|
| v1–v3 | Protótipo inicial; marcação por três toques; data por EXIF e por nome de arquivo |
| v4–v7 | Banco de modelos de coletor; leitura por periodicidade; disparo automático na pré-visualização |
| v8–v9 | OCR dos números da escala; orientação EXIF corrigida; recusa sem número reconhecido |
| v10 | Detecção de tinta por contraste local, independente de cor e fabricante |
| v11 | Pilha de traços como método principal; OCR como conferência; contagem de baixo só com pilha completa |
| v12 | Cadastro do coletor por máxima, mínima e escala; recusa abaixo da menor marca |
| v13 | Crivo de qualidade (contraste, nitidez, inclinação, corte); registro "< mínima"; escala cortada é recusa |
| v14 | Correção: oito funções apagadas por engano em v10–v13 (diálogo de captura, EXIF, débito, avisos) restauradas; testes de interface automatizados adicionados |

## Licença

MIT.
