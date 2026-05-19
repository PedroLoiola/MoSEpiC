<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="MoSEpiC — Modelo de Simulação Epidemiológica por Compartimentos: simulador didático SIR/SEIR por autômatos celulares com recursos de reprodutibilidade." />
  <meta name="author" content="Pedro Loiola" />
  <title>MoSEpiC | Modelo de Simulação Epidemiológica por Compartimentos</title>
  <link rel="stylesheet" href="src/styles.css" />
</head>
<body>
  <header>
    <section class="hero" aria-labelledby="titulo-principal">
      <div class="logo-box" aria-label="Logo do MoSEpiC">
        <img src="assets/logo-mosepic.png" alt="Logo do MoSEpiC" />
      </div>

      <div>
        <h1 id="titulo-principal"><span class="mark">MoSEpiC</span></h1>
        <p class="project-name">Modelo de Simulação Epidemiológica por Compartimentos</p>
        <p class="short-description">
          Simulador didático de epidemias baseado em modelos compartimentais SIR/SEIR e autômatos celulares.
          O aplicativo permite explorar como transmissão, recuperação, incubação, vizinhança espacial e aleatoriedade
          influenciam a dinâmica epidêmica em uma população distribuída em grade.
        </p>
      </div>
    </section>
  </header>

  <main>
    <aside class="card controls" aria-label="Painel de parâmetros da simulação">
      <p class="section-title">PARÂMETROS DO MODELO</p>

      <div class="control">
        <label for="model">MODELO <span id="modelValue">SEIR</span></label>
        <select id="model">
          <option value="sir">SIR — Suscetível, Infectado, Recuperado</option>
          <option value="seir" selected>SEIR — Suscetível, Exposto, Infectado, Recuperado</option>
        </select>
      </div>

      <div class="control">
        <label for="preset">CENÁRIO EPIDEMIOLÓGICO <span id="presetValue">Personalizado</span></label>
        <select id="preset">
          <option value="custom" selected>Personalizado</option>
          <option value="influenza">Influenza sazonal — aproximação didática</option>
          <option value="covid19">COVID-19 inicial — aproximação didática</option>
          <option value="sarampo">Sarampo — aproximação didática</option>
          <option value="ebola">Ebola — aproximação didática</option>
        </select>
        <p class="help-line">Os presets são aproximações didáticas e não substituem calibração empírica local.</p>
      </div>

      <div class="control">
        <label for="population">POPULAÇÃO <span id="populationValue"></span></label>
        <input id="population" type="range" min="100" max="10000" step="100" value="2500" />
        <input id="populationNumber" type="number" min="10" max="10000" step="10" value="2500" />
      </div>

      <div class="control">
        <label for="initialInfected">INFECTADOS INICIAIS <span id="initialInfectedValue"></span></label>
        <input id="initialInfected" type="range" min="1" max="250" step="1" value="12" />
        <input id="initialInfectedNumber" type="number" min="1" max="10000" step="1" value="12" />
      </div>

      <div class="control">
        <label for="neighborhood">ALGORITMO DE VIZINHANÇA <span id="neighborhoodValue">Moore</span></label>
        <select id="neighborhood">
          <option value="moore" selected>Moore — 8 vizinhos</option>
          <option value="vonneumann">Von Neumann — 4 vizinhos</option>
        </select>
      </div>

      <div class="control">
        <label for="seed">SEMENTE ALEATÓRIA <span id="seedValue"></span></label>
        <div class="two-inputs seed-row">
          <input id="seed" type="text" value="mosepic-2026" aria-label="Semente aleatória" />
          <button id="newSeed" type="button">Gerar</button>
        </div>
        <p class="help-line">Use a mesma semente e os mesmos parâmetros para reproduzir uma simulação.</p>
      </div>

      <div class="control">
        <label for="r0">R0 — ALVO DIDÁTICO EM POPULAÇÃO SUSCETÍVEL <span id="r0Value"></span></label>
        <input id="r0" type="range" min="0.1" max="20" step="0.1" value="2.0" />
        <p class="help-line">Ao alterar R0, o app ajusta β local usando a vizinhança selecionada.</p>
      </div>

      <div class="control">
        <label for="beta">TAXA DE TRANSMISSÃO LOCAL β <span id="betaValue"></span></label>
        <input id="beta" type="range" min="0" max="1" step="0.001" value="0.036" />
        <p class="help-line">Probabilidade diária de transmissão por contato local com vizinho infectante.</p>
      </div>

      <div class="control">
        <label for="gamma">TAXA DE RECUPERAÇÃO γ <span id="gammaValue"></span></label>
        <input id="gamma" type="range" min="0.01" max="1" step="0.001" value="0.143" />
        <p class="help-line">Tempo médio de infecção = <strong>1/γ</strong>: <span id="infectiousMeanValue"></span>.</p>
      </div>

      <div class="control" id="sigmaControl">
        <label for="sigma">TAXA DE INCUBAÇÃO σ <span id="sigmaValue"></span></label>
        <input id="sigma" type="range" min="0.01" max="1" step="0.001" value="0.200" />
        <p class="help-line">Tempo médio de incubação = <strong>1/σ</strong>: <span id="incubationMeanValue"></span>.</p>
      </div>

      <div class="control">
        <label for="maxDays">TEMPO MÁXIMO DA SIMULAÇÃO <span id="maxDaysValue"></span></label>
        <input id="maxDays" type="range" min="10" max="365" step="5" value="160" />
      </div>

      <div class="control">
        <label for="speed">VELOCIDADE DA ANIMAÇÃO <span id="speedValue"></span></label>
        <input id="speed" type="range" min="1" max="20" step="1" value="7" />
      </div>

      <div class="derived-box" aria-label="Resumo dos parâmetros derivados">
        <div><strong>R0 aproximado local:</strong> <span id="derivedR0">0,00</span></div>
        <div><strong>1/γ:</strong> <span id="derivedInfectious">0 dia</span></div>
        <div id="derivedSigmaLine"><strong>1/σ:</strong> <span id="derivedIncubation">0 dia</span></div>
        <div><strong>Unidade temporal:</strong> 1 passo = 1 dia</div>
      </div>

      <div class="buttons">
        <button class="primary" id="toggle" type="button">Iniciar</button>
        <button id="step" type="button">Avançar 1 dia</button>
        <button id="reset" type="button">Resetar</button>
      </div>

      <div class="buttons export-buttons">
        <button id="downloadChart" type="button">Baixar gráfico PNG</button>
        <button id="downloadCSV" type="button">Baixar dados CSV</button>
        <button id="downloadJSON" type="button">Baixar parâmetros JSON</button>
      </div>

      <div class="status" id="statusBox">
        Ajuste os parâmetros, defina a semente aleatória e clique em iniciar. A simulação pausa automaticamente quando não há mais indivíduos expostos ou infectados, ou quando o tempo máximo é atingido.
      </div>
    </aside>

    <section class="workspace" aria-label="Área de simulação e explicações">
      <div class="metrics" aria-label="Métricas da simulação">
        <div class="card metric">
          <div class="name">Suscetíveis</div>
          <div class="value" id="sCount">0</div>
          <div class="pct" id="sPct">0%</div>
        </div>
        <div class="card metric" id="eMetric">
          <div class="name">Expostos</div>
          <div class="value" id="eCount">0</div>
          <div class="pct" id="ePct">0%</div>
        </div>
        <div class="card metric">
          <div class="name">Infectados</div>
          <div class="value" id="iCount">0</div>
          <div class="pct" id="iPct">0%</div>
        </div>
        <div class="card metric">
          <div class="name">Recuperados</div>
          <div class="value" id="rCount">0</div>
          <div class="pct" id="rPct">0%</div>
        </div>
        <div class="card metric">
          <div class="name">Pico de infectados</div>
          <div class="value" id="peakCount">0</div>
          <div class="pct" id="peakDay">Dia 0</div>
        </div>
        <div class="card metric">
          <div class="name">Ataque acumulado</div>
          <div class="value" id="attackRate">0%</div>
          <div class="pct" id="dayCount">Dia 0</div>
        </div>
      </div>

      <div class="visual-grid">
        <div class="card canvas-card">
          <div class="section-row">
            <p class="section-title">GRADE ESPACIAL</p>
            <p class="section-subtitle">Cada célula representa um indivíduo.</p>
          </div>
          <div class="canvas-wrap">
            <canvas id="gridCanvas" aria-label="Grade espacial da simulação"></canvas>
          </div>
          <div class="legend">
            <span class="legend-item"><span class="dot" style="background: var(--s)"></span>Suscetível</span>
            <span class="legend-item" id="eLegend"><span class="dot" style="background: var(--e)"></span>Exposto</span>
            <span class="legend-item"><span class="dot" style="background: var(--i)"></span>Infectado</span>
            <span class="legend-item"><span class="dot" style="background: var(--r)"></span>Recuperado</span>
          </div>
        </div>

        <div class="card chart-card">
          <div class="section-row">
            <p class="section-title">CURVAS EPIDÊMICAS</p>
            <p class="section-subtitle">Dados exportáveis em CSV.</p>
          </div>
          <div class="chart-wrap">
            <canvas id="chartCanvas" aria-label="Curvas SIR ou SEIR da simulação"></canvas>
          </div>
          <div class="legend">
            <span class="legend-item"><span class="dot" style="background: var(--s)"></span>S</span>
            <span class="legend-item" id="eChartLegend"><span class="dot" style="background: var(--e)"></span>E</span>
            <span class="legend-item"><span class="dot" style="background: var(--i)"></span>I</span>
            <span class="legend-item"><span class="dot" style="background: var(--r)"></span>R</span>
            <span class="legend-item"><span class="dot" style="background: var(--incidence)"></span>Novos casos</span>
          </div>
        </div>
      </div>

      <div class="card text-card">
        <div class="tabs" role="tablist" aria-label="Abas explicativas">
          <button class="tab-btn active" data-tab="parametros" type="button">Parâmetros</button>
          <button class="tab-btn" data-tab="modelos" type="button">SIR e SEIR</button>
          <button class="tab-btn" data-tab="automatos" type="button">Autômatos celulares</button>
          <button class="tab-btn" data-tab="reprodutibilidade" type="button">Reprodutibilidade</button>
          <button class="tab-btn" data-tab="limitacoes" type="button">Limitações e referências</button>
        </div>

        <article id="parametros" class="tab-panel active">
          <h2>Parâmetros epidemiológicos</h2>
          <p>
            O parâmetro <strong>β</strong> representa, neste aplicativo, a probabilidade diária de transmissão local entre uma célula suscetível e um vizinho infectante. Já <strong>γ</strong> é uma taxa diária de recuperação: quanto maior γ, menor tende a ser o tempo médio no compartimento infectado. O tempo médio de infecção é aproximado por <span class="formula-inline">1/γ</span>.
          </p>
          <p>
            No modelo SEIR, <strong>σ</strong> é a taxa diária de transição do estado exposto para o estado infectante. O tempo médio de incubação/latência é aproximado por <span class="formula-inline">1/σ</span>. Em cada dia, o app converte as taxas em probabilidades de transição por <span class="formula-inline">1 − e<sup>−taxa</sup></span>.
          </p>
          <p class="note">
            O <strong>R0</strong> do MoSEpiC é um <strong>alvo didático em população totalmente suscetível</strong>. Como o modelo usa contatos locais em uma grade, ele não deve ser interpretado como estimativa empírica do número reprodutivo básico de uma população real.
          </p>
          <div class="table-wrap">
            <table>
              <thead>
                <tr><th>Cenário</th><th>R0 didático</th><th>Tempo médio de incubação</th><th>Tempo médio de infecção</th><th>Observação</th></tr>
              </thead>
              <tbody>
                <tr><td>Influenza sazonal</td><td>1,4</td><td>1,5 dia</td><td>3 dias</td><td>Valores aproximados para ensino.</td></tr>
                <tr><td>COVID-19 inicial</td><td>2,5</td><td>5,2 dias</td><td>7 dias</td><td>Valores aproximados para ensino.</td></tr>
                <tr><td>Sarampo</td><td>15,0</td><td>10 dias</td><td>8 dias</td><td>Exemplo de alta transmissibilidade.</td></tr>
                <tr><td>Ebola</td><td>1,8</td><td>9 dias</td><td>10 dias</td><td>Valores aproximados para ensino.</td></tr>
              </tbody>
            </table>
          </div>
        </article>

        <article id="modelos" class="tab-panel">
          <h2>Modelos SIR e SEIR</h2>
          <p>
            Modelos compartimentais agrupam indivíduos conforme seu estado epidemiológico. O modelo SIR usa três compartimentos: suscetíveis, infectados e recuperados. O modelo SEIR adiciona o compartimento exposto, representando indivíduos infectados que ainda não transmitem ou ainda não entraram plenamente no período infectante.
          </p>
          <h3>Equações diferenciais do modelo SIR clássico</h3>
          <div class="equation">
            dS/dt = −βSI/N<br />
            dI/dt = βSI/N − γI<br />
            dR/dt = γI
          </div>
          <h3>Equações diferenciais do modelo SEIR clássico</h3>
          <div class="equation">
            dS/dt = −βSI/N<br />
            dE/dt = βSI/N − σE<br />
            dI/dt = σE − γI<br />
            dR/dt = γI
          </div>
          <p>
            O MoSEpiC não resolve diretamente essas equações diferenciais. Ele traduz a lógica compartimental para uma simulação espacial em autômato celular, na qual cada indivíduo ocupa uma célula e interage com vizinhos locais.
          </p>
        </article>

        <article id="automatos" class="tab-panel">
          <h2>Autômatos celulares</h2>
          <p>
            Um autômato celular representa um sistema formado por células que mudam de estado ao longo do tempo conforme regras locais. No MoSEpiC, cada célula pode estar nos estados S, E, I ou R. A transmissão ocorre quando uma célula suscetível possui vizinhos infectantes.
          </p>
          <p>
            O algoritmo de <strong>Moore</strong> considera oito vizinhos ao redor da célula, incluindo diagonais. O algoritmo de <strong>Von Neumann</strong> considera quatro vizinhos ortogonais. Assim, Moore aumenta a conectividade local e tende a facilitar a propagação, enquanto Von Neumann torna os contatos mais restritos.
          </p>
          <p>
            Para conectar o R0 didático ao autômato celular, o app usa uma aproximação local: <span class="formula-inline">R0 ≈ β × k / γ</span>, em que <span class="formula-inline">k</span> é o número máximo de vizinhos considerados. Essa aproximação é útil para ensino, mas não substitui estimativas formais baseadas em dados reais.
          </p>
        </article>

        <article id="reprodutibilidade" class="tab-panel">
          <h2>Reprodutibilidade</h2>
          <p>
            O MoSEpiC usa aleatoriedade para distribuir infectados iniciais e para simular transmissões e transições de estado. Para tornar a simulação reprodutível, o usuário pode definir uma <strong>semente aleatória</strong>. A mesma semente, com os mesmos parâmetros e a mesma versão do software, deve produzir a mesma trajetória simulada.
          </p>
          <ul>
            <li><strong>PNG:</strong> exporta a imagem da curva epidemiológica.</li>
            <li><strong>CSV:</strong> exporta a série temporal com S, E, I, R, novos casos e ataque acumulado.</li>
            <li><strong>JSON:</strong> exporta parâmetros, versão, semente, preset, modelo e algoritmo usados.</li>
          </ul>
          <p>
            Para fins acadêmicos, recomenda-se citar a versão do software, armazenar os arquivos CSV/JSON e, quando possível, arquivar uma release do repositório em plataforma como Zenodo.
          </p>
        </article>

        <article id="limitacoes" class="tab-panel">
          <h2>Limitações e base científica</h2>
          <p>
            Este aplicativo possui finalidade didática e exploratória. Ele não deve ser usado para previsão epidemiológica, vigilância em tempo real ou decisão sanitária sem calibração empírica, validação e revisão especializada. A versão atual não inclui idade, vacinação, mortalidade, assintomáticos, reinfecção, estrutura domiciliar, redes reais de contato, mobilidade territorial ou heterogeneidade socioambiental.
          </p>
          <ul class="refs">
            <li>KERMACK, W. O.; MCKENDRICK, A. G. A contribution to the mathematical theory of epidemics. <em>Proceedings of the Royal Society A</em>, 1927.</li>
            <li>HETHCOTE, H. W. The mathematics of infectious diseases. <em>SIAM Review</em>, 2000.</li>
            <li>ANDERSON, R. M.; MAY, R. M. <em>Infectious Diseases of Humans: Dynamics and Control</em>. Oxford University Press, 1991.</li>
            <li>SIRAKOULIS, G. C.; KARAFYLLIDIS, I.; THANAILAKIS, A. A cellular automaton model for the effects of population movement and vaccination on epidemic propagation. <em>Ecological Modelling</em>, 2000.</li>
            <li>WHITE, S. H.; DEL REY, A. M.; SÁNCHEZ, G. R. Modeling epidemics using cellular automata. <em>Applied Mathematics and Computation</em>, 2007.</li>
          </ul>
        </article>
      </div>

      <footer class="card footer-card">
        <div class="license-block">
          <img src="assets/cc-by-nc-sa.png" alt="Licença Creative Commons BY-NC-SA" />
          <p>
            Creative Commons Atribuição-NãoComercial-CompartilhaIgual 4.0 Internacional — CC BY-NC-SA 4.0.
            Você pode compartilhar e adaptar o material para fins não comerciais, desde que atribua a autoria e distribua obras derivadas sob a mesma licença.
          </p>
        </div>
        <p class="educational-note">
          Aplicativo com finalidade didática. Os resultados são simulações simplificadas e não devem ser interpretados como previsão epidemiológica real.
        </p>
      </footer>
    </section>
  </main>

  <script src="src/app.js"></script>
</body>
</html>
