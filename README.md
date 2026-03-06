# siachoque.github.io
hola a todos , creado para ustedes 
proyecto comunitario 
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Software de Seguimiento de Pacientes</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f4f7f6;
        }
        .chart-container {
            position: relative;
            height: 300px;
            width: 100%;
        }
        .card {
            background-color: white;
            border-radius: 0.75rem;
            box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
            transition: all 0.3s ease-in-out;
        }
        .card:hover {
            box-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -2px rgb(0 0 0 / 0.1);
        }
        .btn-primary {
            background-color: #007bff;
            color: white;
            font-weight: 500;
            padding: 0.6rem 1.2rem;
            border-radius: 0.5rem;
            transition: background-color 0.3s;
        }
        .btn-primary:hover {
            background-color: #0056b3;
        }
         .btn-secondary {
            background-color: #6c757d;
            color: white;
        }
        .btn-secondary:hover {
            background-color: #5a6268;
        }
        .btn-danger {
            background-color: #dc3545; color: white;
            padding: 0.25rem 0.5rem; border-radius: 0.25rem;
            font-size: 0.8rem;
        }
        .btn-danger:hover { background-color: #c82333; }
        .patient-list-item.active {
            background-color: #e0f2fe;
            border-left: 4px solid #007bff;
        }
        .modal {
            display: none; position: fixed; z-index: 1000;
            left: 0; top: 0; width: 100%; height: 100%;
            overflow: auto; background-color: rgba(0,0,0,0.5);
            align-items: center; justify-content: center;
        }
        .modal-content {
            background-color: #fefefe; margin: auto;
            padding: 2rem; border: 1px solid #888;
            width: 90%; max-width: 800px; border-radius: 0.75rem;
        }
        .close-button {
            color: #aaa; float: right; font-size: 28px;
            font-weight: bold; cursor: pointer;
        }
        .close-button:hover, .close-button:focus {
            color: black; text-decoration: none; cursor: pointer;
        }
        .tab-button {
            padding: 0.5rem 1rem; border-radius: 0.5rem;
            cursor: pointer; font-weight: 500;
            transition: all 0.3s;
        }
        .tab-button.active {
            background-color: #007bff;
            color: white;
        }
        .tab-button:not(.active) {
            background-color: #e9ecef;
            color: #495057;
        }
        fieldset {
            border: 1px solid #dee2e6;
        }
        legend {
            color: #495057;
        }
        /* Custom styles for radio button labels to look like boxes */
        .morisky-label {
            display: block;
            padding: 0.75rem;
            border: 2px solid #e2e8f0;
            border-radius: 0.375rem;
            text-align: center;
            cursor: pointer;
            transition: all 0.2s ease-in-out;
            flex: 1;
        }
        .morisky-label:hover {
            background-color: #f7fafc;
            border-color: #cbd5e0;
        }
        .morisky-radio:checked + .morisky-label {
            background-color: #3b82f6;
            border-color: #2563eb;
            color: white;
            font-weight: 600;
        }
    </style>
</head>
<body>

    <header class="bg-white shadow-md">
        <div class="container mx-auto px-6 py-4">
            <h1 class="text-3xl font-bold text-gray-800">Plataforma de Seguimiento Geriátrico</h1>
            <p class="text-gray-600">Gestión y visualización de escalas de valoración integral.</p>
        </div>
    </header>

    <main class="container mx-auto px-6 py-8">
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <div class="lg:col-span-1">
                <div class="card p-6">
                    <h2 class="text-2xl font-bold text-gray-700 mb-4">Gestión de Pacientes</h2>
                    <button id="add-patient-btn" class="w-full btn-primary mb-4">Agregar Nuevo Paciente</button>
                    <div>
                        <h3 class="text-lg font-semibold text-gray-600 mb-2">Lista de Pacientes</h3>
                        <ul id="patient-list" class="space-y-2">
                             <li class="text-center text-gray-500 py-4">No hay pacientes registrados.</li>
                        </ul>
                    </div>
                </div>
            </div>

            <div class="lg:col-span-2">
                 <div class="flex space-x-2 mb-4 border-b pb-2">
                    <button id="tab-patient-details" class="tab-button active">Detalles del Paciente</button>
                    <button id="tab-aggregated-data" class="tab-button">Datos Agregados</button>
                    <button id="tab-scales-format" class="tab-button">Formatos de Escalas</button>
                </div>

                <div id="patient-view">
                    <div id="patient-details-section" class="card p-6 hidden">
                        <div class="flex justify-between items-start">
                            <div>
                                <h2 id="patient-name-header" class="text-3xl font-bold text-blue-600"></h2>
                                <p id="patient-info-header" class="text-gray-500"></p>
                            </div>
                            <div class="flex space-x-2">
                                <button id="edit-history-btn" class="btn-secondary btn-primary">Editar Historia</button>
                                <button id="view-history-btn" class="btn-secondary btn-primary">Ver Historia Clínica</button>
                                <button id="add-record-btn" class="btn-primary">Registrar Medición</button>
                            </div>
                        </div>
                        <hr class="my-6">
                        <div id="charts-container" class="grid grid-cols-1 md:grid-cols-2 gap-6">
                           <div class="card p-4"><h3 class="font-semibold text-center text-gray-700">Fragilidad (SPPB)</h3><div class="chart-container"><canvas id="fragilityChart"></canvas></div></div>
                           <div class="card p-4"><h3 class="font-semibold text-center text-gray-700">Riesgo de Caída (Up and Go)</h3><div class="chart-container"><canvas id="fallRiskChart"></canvas></div></div>
                           <div class="card p-4"><h3 class="font-semibold text-center text-gray-700">Valoración Cognitiva (Pfeiffer)</h3><div class="chart-container"><canvas id="cognitiveChart"></canvas></div></div>
                           <div class="card p-4"><h3 class="font-semibold text-center text-gray-700">Adherencia Farmacológica (Morisky)</h3><div class="chart-container"><canvas id="adherenceChart"></canvas></div></div>
                           <div class="card p-4 md:col-span-2"><h3 class="font-semibold text-center text-gray-700">Riesgo Cardiovascular (Framingham %)</h3><div class="chart-container"><canvas id="cardioRiskChart"></canvas></div></div>
                        </div>
                        <div id="records-list-section" class="mt-8"></div>
                    </div>
                    <div id="welcome-message" class="card p-10 text-center">
                        <h2 class="text-2xl font-semibold text-gray-700">Bienvenido</h2>
                        <p class="text-gray-500 mt-2">Seleccione un paciente para ver sus datos o agregue uno nuevo para comenzar.</p>
                    </div>
                </div>
                
                <div id="aggregated-view" class="hidden">
                     <div class="card p-6">
                        <h2 class="text-2xl font-bold text-gray-700 mb-4">Promedio de Escalas (Todos los Pacientes)</h2>
                         <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                           <div class="card p-4"><h3 class="font-semibold text-center text-gray-700">Fragilidad (SPPB)</h3><div class="chart-container"><canvas id="aggFragilityChart"></canvas></div></div>
                           <div class="card p-4"><h3 class="font-semibold text-center text-gray-700">Riesgo de Caída (Up and Go)</h3><div class="chart-container"><canvas id="aggFallRiskChart"></canvas></div></div>
                           <div class="card p-4"><h3 class="font-semibold text-center text-gray-700">Valoración Cognitiva (Pfeiffer)</h3><div class="chart-container"><canvas id="aggCognitiveChart"></canvas></div></div>
                           <div class="card p-4"><h3 class="font-semibold text-center text-gray-700">Adherencia Farmacológica (Morisky)</h3><div class="chart-container"><canvas id="aggAdherenceChart"></canvas></div></div>
                           <div class="card p-4 md:col-span-2"><h3 class="font-semibold text-center text-gray-700">Riesgo Cardiovascular (Framingham %)</h3><div class="chart-container"><canvas id="aggCardioRiskChart"></canvas></div></div>
                        </div>
                     </div>
                </div>
                
                <div id="scales-format-view" class="hidden">
                    <div class="card p-6">
                        <h2 class="text-2xl font-bold text-gray-700 mb-4">Formatos de Escalas</h2>
                        <div class="space-y-6">
                            <div>
                                <h3 class="text-xl font-semibold text-blue-600">Test de Adherencia a la Medicación de Morisky-Green</h3>
                                <p class="text-gray-600 mt-1">Este test evalúa el cumplimiento del tratamiento farmacológico.</p>
                                <ul class="list-decimal list-inside mt-4 space-y-2 text-gray-700">
                                    <li>¿Olvida alguna vez tomar los medicamentos? <span class="font-semibold text-green-600">(Respuesta correcta: No)</span></li>
                                    <li>¿Toma los medicamentos a las horas indicadas? <span class="font-semibold text-green-600">(Respuesta correcta: Sí)</span></li>
                                    <li>Cuando se encuentra bien, ¿deja de tomar la medicación? <span class="font-semibold text-green-600">(Respuesta correcta: No)</span></li>
                                    <li>Si alguna vez le sienta mal, ¿deja de tomarla? <span class="font-semibold text-green-600">(Respuesta correcta: No)</span></li>
                                </ul>
                                <div class="mt-4 p-4 bg-gray-50 rounded-lg">
                                    <h4 class="font-semibold">Interpretación de los resultados:</h4>
                                    <p class="mt-2"><strong class="text-green-700">Cumplidor:</strong> El paciente responde de forma correcta a las cuatro preguntas (No/Sí/No/No).</p>
                                    <p class="mt-1"><strong class="text-red-700">Incumplidor:</strong> El paciente responde con un "Sí" a la pregunta 1, 3 o 4, o con un "No" a la pregunta 2.</p>
                                </div>
                            </div>
                            <!-- Se pueden agregar más escalas aquí en el futuro -->
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </main>

    <!-- Modals -->
    <div id="patient-modal" class="modal">
        <div class="modal-content" style="max-height: 90vh; overflow-y: auto;">
            <span class="close-button" id="close-patient-modal">&times;</span>
            <h2 class="text-2xl font-bold text-gray-700 mb-4">Formulario de Historia Clínica</h2>
            <form id="patient-form" class="space-y-4">
                <input type="hidden" id="patient-edit-id">
                <fieldset class="border p-4 rounded-md">
                    <legend class="px-2 font-semibold text-gray-600">Datos de Identificación</legend>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div><label for="patient-name" class="block text-sm font-medium text-gray-700">Nombre Completo</label><input type="text" id="patient-name" class="mt-1 w-full p-2 border rounded-md" required></div>
                        <div><label for="patient-id" class="block text-sm font-medium text-gray-700">Número de Documento</label><input type="text" id="patient-id" class="mt-1 w-full p-2 border rounded-md" required></div>
                        <div><label for="patient-birthdate" class="block text-sm font-medium text-gray-700">Fecha de Nacimiento</label><input type="date" id="patient-birthdate" class="mt-1 w-full p-2 border rounded-md" required></div>
                        <div><label for="patient-birthplace" class="block text-sm font-medium text-gray-700">Lugar de Nacimiento</label><input type="text" id="patient-birthplace" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="patient-address" class="block text-sm font-medium text-gray-700">Dirección</label><input type="text" id="patient-address" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="patient-phone" class="block text-sm font-medium text-gray-700">Teléfono</label><input type="tel" id="patient-phone" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="patient-sex" class="block text-sm font-medium text-gray-700">Sexo</label><select id="patient-sex" class="mt-1 w-full p-2 border rounded-md"><option value="Femenino">Femenino</option><option value="Masculino">Masculino</option></select></div>
                        <div><label for="patient-civil-status" class="block text-sm font-medium text-gray-700">Estado Civil</label><input type="text" id="patient-civil-status" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="patient-occupation" class="block text-sm font-medium text-gray-700">Ocupación</label><input type="text" id="patient-occupation" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="patient-schooling" class="block text-sm font-medium text-gray-700">Escolaridad</label><input type="text" id="patient-schooling" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="patient-eapbs" class="block text-sm font-medium text-gray-700">EAPBS</label><input type="text" id="patient-eapbs" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="patient-regimen" class="block text-sm font-medium text-gray-700">Régimen</label><input type="text" id="patient-regimen" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div class="md:col-span-2"><label for="patient-family-structure" class="block text-sm font-medium text-gray-700">Estructura Familiar</label><textarea id="patient-family-structure" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                    </div>
                </fieldset>

                <fieldset class="border p-4 rounded-md">
                    <legend class="px-2 font-semibold text-gray-600">Consulta Actual</legend>
                    <div><label for="patient-consult-reason" class="block text-sm font-medium text-gray-700">Motivo de Consulta</label><textarea id="patient-consult-reason" rows="3" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                    <div><label for="patient-current-illness" class="block text-sm font-medium text-gray-700">Enfermedad Actual</label><textarea id="patient-current-illness" rows="3" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                </fieldset>

                <fieldset class="border p-4 rounded-md">
                    <legend class="px-2 font-semibold text-gray-600">Antecedentes</legend>
                    <div class="space-y-4">
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div><label for="antecedentes-patologicos" class="block text-sm font-medium text-gray-700">Patológicos</label><textarea id="antecedentes-patologicos" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-quirurgicos" class="block text-sm font-medium text-gray-700">Quirúrgicos</label><textarea id="antecedentes-quirurgicos" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-farmacologicos" class="block text-sm font-medium text-gray-700">Farmacológicos</label><textarea id="antecedentes-farmacologicos" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-toxicos" class="block text-sm font-medium text-gray-700">Tóxicos (Biomasa, Tabaco, IPA, Sustancias)</label><textarea id="antecedentes-toxicos" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-familiares" class="block text-sm font-medium text-gray-700">Familiares</label><textarea id="antecedentes-familiares" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-psiquiatricos" class="block text-sm font-medium text-gray-700">Psiquiátricos</label><textarea id="antecedentes-psiquiatricos" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-transfusionales" class="block text-sm font-medium text-gray-700">Transfusionales</label><textarea id="antecedentes-transfusionales" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-hemoclasificacion" class="block text-sm font-medium text-gray-700">Hemoclasificación</label><input type="text" id="antecedentes-hemoclasificacion" class="mt-1 w-full p-2 border rounded-md"></div>
                            <div><label for="antecedentes-alergicos" class="block text-sm font-medium text-gray-700">Alérgicos</label><textarea id="antecedentes-alergicos" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-ocupacionales" class="block text-sm font-medium text-gray-700">Ocupacionales (Actual y Anteriores)</label><textarea id="antecedentes-ocupacionales" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-inmunologicos" class="block text-sm font-medium text-gray-700">Inmunológicos</label><textarea id="antecedentes-inmunologicos" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                            <div><label for="antecedentes-ets" class="block text-sm font-medium text-gray-700">Enfermedades de Trasmisión Sexual</label><textarea id="antecedentes-ets" rows="2" class="mt-1 w-full p-2 border rounded-md"></textarea></div>
                             <div><label for="antecedentes-vif" class="block text-sm font-medium text-gray-700">Violencia Intrafamiliar</label><select id="antecedentes-vif" class="mt-1 w-full p-2 border rounded-md"><option value="No">No</option><option value="Si">Sí</option></select></div>
                             <div><label for="patient-housing-type" class="block text-sm font-medium text-gray-700">Tipo de Vivienda</label><input type="text" id="patient-housing-type" class="mt-1 w-full p-2 border rounded-md"></div>
                        </div>
                    </div>
                </fieldset>

                <fieldset id="ginecologicos-fieldset" class="border p-4 rounded-md">
                    <legend class="px-2 font-semibold text-gray-600">Antecedentes Ginecológicos</legend>
                    <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                        <div><label for="ginecologicos-fur" class="block text-sm font-medium text-gray-700">FUR</label><input type="date" id="ginecologicos-fur" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="ginecologicos-g" class="block text-sm font-medium text-gray-700">G</label><input type="number" id="ginecologicos-g" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="ginecologicos-p" class="block text-sm font-medium text-gray-700">P</label><input type="number" id="ginecologicos-p" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="ginecologicos-v" class="block text-sm font-medium text-gray-700">V</label><input type="number" id="ginecologicos-v" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="ginecologicos-a" class="block text-sm font-medium text-gray-700">A</label><input type="number" id="ginecologicos-a" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div><label for="ginecologicos-c" class="block text-sm font-medium text-gray-700">C</label><input type="number" id="ginecologicos-c" class="mt-1 w-full p-2 border rounded-md"></div>
                        <div class="col-span-2"><label for="ginecologicos-planificacion" class="block text-sm font-medium text-gray-700">Planificación</label><input type="text" id="ginecologicos-planificacion" class="mt-1 w-full p-2 border rounded-md"></div>
                    </div>
                </fieldset>
                
                <button type="submit" class="w-full btn-primary !mt-6">Guardar Paciente</button>
            </form>
        </div>
    </div>
    
    <div id="record-modal" class="modal">
        <div class="modal-content" style="max-height: 90vh; overflow-y: auto;">
            <span class="close-button" id="close-record-modal">&times;</span>
            <h2 class="text-2xl font-bold text-gray-700 mb-4">Nueva Medición para <span id="record-modal-patient-name" class="text-blue-600"></span></h2>
            <form id="record-form" class="space-y-4">
                <div><label for="record-date" class="block text-gray-700">Fecha de Medición</label><input type="date" id="record-date" class="w-full p-2 border rounded mt-1" required></div>
                <div class="border p-3 rounded"><label for="fragility-score" class="block font-semibold text-gray-700">Fragilidad (SPPB) (0-12)</label><input type="number" step="any" id="fragility-score" min="0" max="12" class="w-full p-2 border rounded mt-1" required><p class="text-xs text-gray-500 mt-1">Interpretación: 0-3 Frágil, 4-9 Pre-frágil, 10-12 No frágil.</p></div>
                <div class="border p-3 rounded"><label for="fall-risk-score" class="block font-semibold text-gray-700">Riesgo de Caída (Up and Go) (segundos)</label><input type="number" step="any" id="fall-risk-score" min="0" class="w-full p-2 border rounded mt-1" required><p class="text-xs text-gray-500 mt-1">Interpretación: >12 segundos = Riesgo Alto.</p></div>
                <div class="border p-3 rounded"><label for="cognitive-score" class="block font-semibold text-gray-700">Valoración Cognitiva (Pfeiffer) (N° de errores, 0-10)</label><input type="number" step="any" id="cognitive-score" min="0" max="10" class="w-full p-2 border rounded mt-1" required><p class="text-xs text-gray-500 mt-1">Interpretación: 0-2 Normal, 3-4 Leve, 5-7 Moderado, 8-10 Severo.</p></div>
                
                <fieldset class="border p-3 rounded">
                    <legend class="px-2 font-semibold text-gray-700">Adherencia Farmacológica (Test de Morisky-Green)</legend>
                    <div class="space-y-3 mt-2" id="morisky-form">
                        <div class="morisky-question">
                            <p class="text-sm text-gray-800">1. ¿Olvida alguna vez tomar los medicamentos?</p>
                            <div class="flex space-x-4 mt-1">
                                <input type="radio" name="morisky-q1" value="si" id="morisky-q1-si" class="sr-only morisky-radio">
                                <label for="morisky-q1-si" class="morisky-label">Sí</label>
                                <input type="radio" name="morisky-q1" value="no" id="morisky-q1-no" class="sr-only morisky-radio">
                                <label for="morisky-q1-no" class="morisky-label">No</label>
                            </div>
                        </div>
                        <div class="morisky-question">
                            <p class="text-sm text-gray-800">2. ¿Toma los medicamentos a las horas indicadas?</p>
                           <div class="flex space-x-4 mt-1">
                                <input type="radio" name="morisky-q2" value="si" id="morisky-q2-si" class="sr-only morisky-radio">
                                <label for="morisky-q2-si" class="morisky-label">Sí</label>
                                <input type="radio" name="morisky-q2" value="no" id="morisky-q2-no" class="sr-only morisky-radio">
                                <label for="morisky-q2-no" class="morisky-label">No</label>
                            </div>
                        </div>
                        <div class="morisky-question">
                            <p class="text-sm text-gray-800">3. Cuando se encuentra bien, ¿deja de tomar la medicación?</p>
                           <div class="flex space-x-4 mt-1">
                                <input type="radio" name="morisky-q3" value="si" id="morisky-q3-si" class="sr-only morisky-radio">
                                <label for="morisky-q3-si" class="morisky-label">Sí</label>
                                <input type="radio" name="morisky-q3" value="no" id="morisky-q3-no" class="sr-only morisky-radio">
                                <label for="morisky-q3-no" class="morisky-label">No</label>
                            </div>
                        </div>
                        <div class="morisky-question">
                            <p class="text-sm text-gray-800">4. Si alguna vez le sienta mal, ¿deja de tomarla?</p>
                            <div class="flex space-x-4 mt-1">
                                <input type="radio" name="morisky-q4" value="si" id="morisky-q4-si" class="sr-only morisky-radio">
                                <label for="morisky-q4-si" class="morisky-label">Sí</label>
                                <input type="radio" name="morisky-q4" value="no" id="morisky-q4-no" class="sr-only morisky-radio">
                                <label for="morisky-q4-no" class="morisky-label">No</label>
                            </div>
                        </div>
                    </div>
                    <div id="morisky-result" class="mt-4 p-2 bg-gray-100 rounded text-center font-semibold text-gray-700">
                        Seleccione una respuesta para cada pregunta.
                    </div>
                    <input type="hidden" id="adherence-score" required>
                </fieldset>
                
                <div class="border p-3 rounded"><label for="cardio-risk-score" class="block font-semibold text-gray-700">Riesgo Cardiovascular (Framingham) (%)</label><input type="number" step="any" id="cardio-risk-score" min="0" max="100" class="w-full p-2 border rounded mt-1" required><p class="text-xs text-gray-500 mt-1">Interpretación: &lt;10% Bajo, 10-20% Moderado, >20% Alto.</p></div>
                <button type="submit" class="w-full btn-primary mt-4">Guardar Medición</button>
            </form>
        </div>
    </div>
    <div id="confirm-modal" class="modal"><div class="modal-content text-center"><h2 class="text-xl font-bold mb-4">Confirmar Eliminación</h2><p id="confirm-modal-text" class="mb-6"></p><div class="flex justify-center space-x-4"><button id="confirm-modal-cancel" class="btn-primary" style="background-color: #6c757d;">Cancelar</button><button id="confirm-modal-confirm" class="btn-danger">Confirmar</button></div></div></div>
    <div id="history-modal" class="modal"><div class="modal-content" style="max-height: 90vh; overflow-y: auto;"><span class="close-button" id="close-history-modal">&times;</span><h2 class="text-2xl font-bold text-gray-700 mb-4">Historia Clínica de <span id="history-patient-name" class="text-blue-600"></span></h2><div id="history-content" class="space-y-4"></div></div></div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            let patients = JSON.parse(localStorage.getItem('patients')) || [];
            let selectedPatientId = null;
            const charts = {};
            let confirmCallback = null;

            const DOM = {
                addPatientBtn: document.getElementById('add-patient-btn'),
                patientModal: document.getElementById('patient-modal'),
                closePatientModal: document.getElementById('close-patient-modal'),
                patientForm: document.getElementById('patient-form'),
                patientList: document.getElementById('patient-list'),
                welcomeMessage: document.getElementById('welcome-message'),
                patientDetailsSection: document.getElementById('patient-details-section'),
                addRecordBtn: document.getElementById('add-record-btn'),
                recordModal: document.getElementById('record-modal'),
                closeRecordModal: document.getElementById('close-record-modal'),
                recordForm: document.getElementById('record-form'),
                recordsListSection: document.getElementById('records-list-section'),
                confirmModal: document.getElementById('confirm-modal'),
                confirmModalText: document.getElementById('confirm-modal-text'),
                confirmModalConfirm: document.getElementById('confirm-modal-confirm'),
                confirmModalCancel: document.getElementById('confirm-modal-cancel'),
                tabPatientDetails: document.getElementById('tab-patient-details'),
                tabAggregatedData: document.getElementById('tab-aggregated-data'),
                tabScalesFormat: document.getElementById('tab-scales-format'),
                patientView: document.getElementById('patient-view'),
                aggregatedView: document.getElementById('aggregated-view'),
                scalesFormatView: document.getElementById('scales-format-view'),
                viewHistoryBtn: document.getElementById('view-history-btn'),
                editHistoryBtn: document.getElementById('edit-history-btn'),
                historyModal: document.getElementById('history-modal'),
                closeHistoryModal: document.getElementById('close-history-modal'),
                historyPatientName: document.getElementById('history-patient-name'),
                historyContent: document.getElementById('history-content'),
                ginecologicosFieldset: document.getElementById('ginecologicos-fieldset'),
                patientSexSelect: document.getElementById('patient-sex'),
                patientEditId: document.getElementById('patient-edit-id'),
                moriskyForm: document.getElementById('morisky-form'),
                moriskyResult: document.getElementById('morisky-result'),
                adherenceScoreInput: document.getElementById('adherence-score'),
            };

            const saveData = () => localStorage.setItem('patients', JSON.stringify(patients));
            const calculateAge = (birthdate) => { const birthDate = new Date(birthdate); const today = new Date(); let age = today.getFullYear() - birthDate.getFullYear(); const m = today.getMonth() - birthDate.getMonth(); if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) { age--; } return age; };
            
            const openConfirmModal = (text, callback) => {
                DOM.confirmModalText.textContent = text;
                confirmCallback = callback;
                DOM.confirmModal.style.display = 'flex';
            };

            const closeConfirmModal = () => {
                DOM.confirmModal.style.display = 'none';
                confirmCallback = null;
            };
            
            const getPatientDataFromForm = () => {
                return { 
                    id: document.getElementById('patient-id').value, 
                    name: document.getElementById('patient-name').value, 
                    birthdate: document.getElementById('patient-birthdate').value,
                    sex: DOM.patientSexSelect.value,
                    history: {
                        birthplace: document.getElementById('patient-birthplace').value,
                        address: document.getElementById('patient-address').value,
                        phone: document.getElementById('patient-phone').value,
                        civilStatus: document.getElementById('patient-civil-status').value,
                        occupation: document.getElementById('patient-occupation').value,
                        schooling: document.getElementById('patient-schooling').value,
                        eapbs: document.getElementById('patient-eapbs').value,
                        regimen: document.getElementById('patient-regimen').value,
                        familyStructure: document.getElementById('patient-family-structure').value,
                        consultReason: document.getElementById('patient-consult-reason').value,
                        currentIllness: document.getElementById('patient-current-illness').value,
                        housingType: document.getElementById('patient-housing-type').value,
                        antecedentes: {
                            patologicos: document.getElementById('antecedentes-patologicos').value,
                            quirurgicos: document.getElementById('antecedentes-quirurgicos').value,
                            farmacologicos: document.getElementById('antecedentes-farmacologicos').value,
                            toxicos: document.getElementById('antecedentes-toxicos').value,
                            familiares: document.getElementById('antecedentes-familiares').value,
                            psiquiatricos: document.getElementById('antecedentes-psiquiatricos').value,
                            transfusionales: document.getElementById('antecedentes-transfusionales').value,
                            hemoclasificacion: document.getElementById('antecedentes-hemoclasificacion').value,
                            alergicos: document.getElementById('antecedentes-alergicos').value,
                            ocupacionales: document.getElementById('antecedentes-ocupacionales').value,
                            inmunologicos: document.getElementById('antecedentes-inmunologicos').value,
                            ets: document.getElementById('antecedentes-ets').value,
                            vif: document.getElementById('antecedentes-vif').value,
                        },
                        ginecologicos: {
                            fur: document.getElementById('ginecologicos-fur').value,
                            g: document.getElementById('ginecologicos-g').value,
                            p: document.getElementById('ginecologicos-p').value,
                            v: document.getElementById('ginecologicos-v').value,
                            a: document.getElementById('ginecologicos-a').value,
                            c: document.getElementById('ginecologicos-c').value,
                            planificacion: document.getElementById('ginecologicos-planificacion').value,
                        }
                    }
                };
            };
            
            const populatePatientForm = (patient) => {
                document.getElementById('patient-id').value = patient.id;
                document.getElementById('patient-name').value = patient.name;
                document.getElementById('patient-birthdate').value = patient.birthdate;
                DOM.patientSexSelect.value = patient.sex;

                const h = patient.history;
                document.getElementById('patient-birthplace').value = h.birthplace;
                document.getElementById('patient-address').value = h.address;
                document.getElementById('patient-phone').value = h.phone;
                document.getElementById('patient-civil-status').value = h.civilStatus;
                document.getElementById('patient-occupation').value = h.occupation;
                document.getElementById('patient-schooling').value = h.schooling;
                document.getElementById('patient-eapbs').value = h.eapbs;
                document.getElementById('patient-regimen').value = h.regimen;
                document.getElementById('patient-family-structure').value = h.familyStructure;
                document.getElementById('patient-consult-reason').value = h.consultReason;
                document.getElementById('patient-current-illness').value = h.currentIllness;
                document.getElementById('patient-housing-type').value = h.housingType;

                const a = h.antecedentes;
                document.getElementById('antecedentes-patologicos').value = a.patologicos;
                document.getElementById('antecedentes-quirurgicos').value = a.quirurgicos;
                document.getElementById('antecedentes-farmacologicos').value = a.farmacologicos;
                document.getElementById('antecedentes-toxicos').value = a.toxicos;
                document.getElementById('antecedentes-familiares').value = a.familiares;
                document.getElementById('antecedentes-psiquiatricos').value = a.psiquiatricos;
                document.getElementById('antecedentes-transfusionales').value = a.transfusionales;
                document.getElementById('antecedentes-hemoclasificacion').value = a.hemoclasificacion;
                document.getElementById('antecedentes-alergicos').value = a.alergicos;
                document.getElementById('antecedentes-ocupacionales').value = a.ocupacionales;
                document.getElementById('antecedentes-inmunologicos').value = a.inmunologicos;
                document.getElementById('antecedentes-ets').value = a.ets;
                document.getElementById('antecedentes-vif').value = a.vif;
                
                const g = h.ginecologicos;
                DOM.ginecologicosFieldset.style.display = patient.sex === 'Femenino' ? 'block' : 'none';
                document.getElementById('ginecologicos-fur').value = g.fur;
                document.getElementById('ginecologicos-g').value = g.g;
                document.getElementById('ginecologicos-p').value = g.p;
                document.getElementById('ginecologicos-v').value = g.v;
                document.getElementById('ginecologicos-a').value = g.a;
                document.getElementById('ginecologicos-c').value = g.c;
                document.getElementById('ginecologicos-planificacion').value = g.planificacion;
            };

            const calculateMoriskyScore = () => {
                const q1 = DOM.moriskyForm.querySelector('input[name="morisky-q1"]:checked');
                const q2 = DOM.moriskyForm.querySelector('input[name="morisky-q2"]:checked');
                const q3 = DOM.moriskyForm.querySelector('input[name="morisky-q3"]:checked');
                const q4 = DOM.moriskyForm.querySelector('input[name="morisky-q4"]:checked');

                if (!q1 || !q2 || !q3 || !q4) {
                    DOM.moriskyResult.textContent = 'Seleccione una respuesta para cada pregunta.';
                    DOM.adherenceScoreInput.value = '';
                    return;
                }

                // Scoring is the number of incorrect answers.
                let score = 0;
                if (q1.value === 'si') score++; // Correct is 'no'
                if (q2.value === 'no') score++; // Correct is 'si'
                if (q3.value === 'si') score++; // Correct is 'no'
                if (q4.value === 'si') score++; // Correct is 'no'

                let interpretation = '';
                if (score === 0) {
                    interpretation = '<span class="text-green-700 font-bold">Cumple</span>';
                    DOM.moriskyResult.className = 'mt-4 p-2 bg-green-100 rounded text-center font-semibold text-green-800';
                } else {
                    interpretation = `<span class="text-red-700 font-bold">No Cumple</span> (Puntaje de incumplimiento: ${score})`;
                    DOM.moriskyResult.className = 'mt-4 p-2 bg-red-100 rounded text-center font-semibold text-red-800';
                }
                
                DOM.adherenceScoreInput.value = score; // Store the numerical score for the chart
                DOM.moriskyResult.innerHTML = `Resultado de Adherencia: ${interpretation}`;
            };

            const renderPatientList = () => {
                DOM.patientList.innerHTML = '';
                if (patients.length === 0) {
                    DOM.patientList.innerHTML = '<li class="text-center text-gray-500 py-4">No hay pacientes registrados.</li>';
                    return;
                }
                patients.forEach(patient => {
                    const age = calculateAge(patient.birthdate);
                    const li = document.createElement('li');
                    li.className = `p-3 rounded-lg cursor-pointer hover:bg-gray-100 patient-list-item flex justify-between items-center ${patient.id === selectedPatientId ? 'active' : ''}`;
                    li.dataset.id = patient.id;
                    li.innerHTML = `
                        <div>
                            <p class="font-semibold text-gray-800">${patient.name}</p>
                            <p class="text-sm text-gray-500">ID: ${patient.id} - ${age} años</p>
                        </div>
                        <button class="btn-danger delete-patient-btn" data-id="${patient.id}">X</button>
                    `;
                    li.addEventListener('click', (e) => {
                        if (e.target.classList.contains('delete-patient-btn')) return;
                        selectedPatientId = patient.id;
                        renderPatientDetails();
                        renderPatientList();
                    });
                    DOM.patientList.appendChild(li);
                });

                document.querySelectorAll('.delete-patient-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        e.stopPropagation();
                        const patientId = e.target.dataset.id;
                        const patient = patients.find(p => p.id === patientId);
                        openConfirmModal(`¿Está seguro que desea eliminar a ${patient.name}? Esta acción no se puede deshacer.`, () => {
                            patients = patients.filter(p => p.id !== patientId);
                            if (selectedPatientId === patientId) {
                                selectedPatientId = null;
                            }
                            saveData();
                            renderPatientList();
                            renderPatientDetails();
                            renderAggregatedCharts();
                            closeConfirmModal();
                        });
                    });
                });
            };

            const renderPatientDetails = () => {
                if (!selectedPatientId) {
                    DOM.welcomeMessage.classList.remove('hidden');
                    DOM.patientDetailsSection.classList.add('hidden');
                    return;
                }
                const patient = patients.find(p => p.id === selectedPatientId);
                if (patient) {
                    DOM.welcomeMessage.classList.add('hidden');
                    DOM.patientDetailsSection.classList.remove('hidden');
                    document.getElementById('patient-name-header').textContent = patient.name;
                    document.getElementById('patient-info-header').textContent = `ID: ${patient.id} - ${calculateAge(patient.birthdate)} años`;
                    document.getElementById('record-modal-patient-name').textContent = patient.name;
                    renderAllCharts(patient);
                    renderRecordsList(patient);
                }
            };
            
            const renderRecordsList = (patient) => {
                DOM.recordsListSection.innerHTML = '<h3 class="text-xl font-bold text-gray-700 mb-4 mt-8">Historial de Mediciones</h3>';
                if (!patient.records || patient.records.length === 0) {
                    DOM.recordsListSection.innerHTML += '<p class="text-gray-500">No hay mediciones registradas para este paciente.</p>';
                    return;
                }
                const table = document.createElement('table');
                table.className = 'w-full text-left border-collapse';
                table.innerHTML = `
                    <thead>
                        <tr class="bg-gray-100">
                            <th class="p-2 border">Fecha</th><th class="p-2 border">Frag.</th><th class="p-2 border">Caída</th>
                            <th class="p-2 border">Cogn.</th><th class="p-2 border">Adher.</th><th class="p-2 border">Cardio.</th><th class="p-2 border">Acción</th>
                        </tr>
                    </thead>
                    <tbody></tbody>`;
                const tbody = table.querySelector('tbody');
                patient.records.forEach((record, index) => {
                    const row = document.createElement('tr');
                    const adherenceText = record.adherence === 0 ? 'Cumple' : `No Cumple (${record.adherence})`;
                    row.innerHTML = `
                        <td class="p-2 border">${new Date(record.date).toLocaleDateString('es-ES')}</td>
                        <td class="p-2 border">${record.fragility}</td><td class="p-2 border">${record.fallRisk}</td>
                        <td class="p-2 border">${record.cognitive}</td><td class="p-2 border">${adherenceText}</td>
                        <td class="p-2 border">${record.cardioRisk}%</td>
                        <td class="p-2 border"><button class="btn-danger delete-record-btn" data-index="${index}">Eliminar</button></td>
                    `;
                    tbody.appendChild(row);
                });
                DOM.recordsListSection.appendChild(table);

                document.querySelectorAll('.delete-record-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        const recordIndex = parseInt(e.target.dataset.index, 10);
                        openConfirmModal('¿Está seguro que desea eliminar esta medición?', () => {
                            patient.records.splice(recordIndex, 1);
                            saveData();
                            renderPatientDetails();
                            renderAggregatedCharts();
                            closeConfirmModal();
                        });
                    });
                });
            };

            const createOrUpdateChart = (chartId, label, labels, data, color) => { const ctx = document.getElementById(chartId).getContext('2d'); if (charts[chartId]) { charts[chartId].destroy(); } charts[chartId] = new Chart(ctx, { type: 'line', data: { labels: labels, datasets: [{ label: label, data: data, borderColor: color, backgroundColor: `${color}33`, tension: 0.1, fill: true, }] }, options: { responsive: true, maintainAspectRatio: false, scales: { y: { beginAtZero: true } }, plugins: { legend: { display: false } } } }); }

            const renderAllCharts = (patient) => {
                const scales = [
                    { key: 'fragility', chartId: 'fragilityChart', label: 'Puntaje Fragilidad', color: '#3B82F6' },
                    { key: 'fallRisk', chartId: 'fallRiskChart', label: 'Tiempo (seg)', color: '#10B981' },
                    { key: 'cognitive', chartId: 'cognitiveChart', label: 'N° de Errores', color: '#F97316' },
                    { key: 'adherence', chartId: 'adherenceChart', label: 'Puntaje Incumplimiento', color: '#8B5CF6' },
                    { key: 'cardioRisk', chartId: 'cardioRiskChart', label: 'Riesgo (%)', color: '#EF4444' }
                ];
                scales.forEach(scale => {
                    const records = (patient.records || []).slice().sort((a, b) => new Date(a.date) - new Date(b.date));
                    const labels = records.map(r => new Date(r.date).toLocaleDateString('es-ES'));
                    const data = records.map(r => r[scale.key]);
                    createOrUpdateChart(scale.chartId, scale.label, labels, data, scale.color);
                });
            };
            
            const renderAggregatedCharts = () => {
                const allRecords = patients.flatMap(p => p.records || []);
                if (allRecords.length === 0) {
                     // Optionally clear charts if no data
                    return;
                }

                const recordsByDate = {};
                allRecords.forEach(rec => {
                    if (!recordsByDate[rec.date]) {
                        recordsByDate[rec.date] = { fragility: [], fallRisk: [], cognitive: [], adherence: [], cardioRisk: [] };
                    }
                    Object.keys(recordsByDate[rec.date]).forEach(key => {
                        if(rec[key] !== undefined) recordsByDate[rec.date][key].push(rec[key])
                    });
                });
                
                const sortedDates = Object.keys(recordsByDate).sort((a,b) => new Date(a) - new Date(b));
                const labels = sortedDates.map(d => new Date(d).toLocaleDateString('es-ES'));
                
                const avg = (arr) => arr.length ? arr.reduce((a, b) => a + b, 0) / arr.length : 0;

                const scales = [
                    { key: 'fragility', chartId: 'aggFragilityChart', label: 'Promedio Fragilidad', color: '#3B82F6' },
                    { key: 'fallRisk', chartId: 'aggFallRiskChart', label: 'Promedio Tiempo (seg)', color: '#10B981' },
                    { key: 'cognitive', chartId: 'aggCognitiveChart', label: 'Promedio Errores', color: '#F97316' },
                    { key: 'adherence', chartId: 'aggAdherenceChart', label: 'Promedio Incumplimiento', color: '#8B5CF6' },
                    { key: 'cardioRisk', chartId: 'aggCardioRiskChart', label: 'Promedio Riesgo (%)', color: '#EF4444' }
                ];

                scales.forEach(scale => {
                    const data = sortedDates.map(date => avg(recordsByDate[date][scale.key]));
                    createOrUpdateChart(scale.chartId, scale.label, labels, data.map(d => d.toFixed(2)), scale.color);
                });
            };

            const renderHistoryModal = (patient) => {
                DOM.historyPatientName.textContent = patient.name;
                
                const createDetail = (label, value) => `<div class="py-2 sm:grid sm:grid-cols-3 sm:gap-4"><dt class="text-sm font-medium text-gray-500">${label}</dt><dd class="mt-1 text-sm text-gray-900 sm:mt-0 sm:col-span-2">${value || 'No registrado'}</dd></div>`;
                
                const gineco = patient.history.ginecologicos;
                const ginecoHtml = patient.sex === 'Femenino' ? `
                    <h3 class="text-lg font-semibold text-gray-800 mt-4 border-b pb-1">Antecedentes Ginecológicos</h3>
                    <div class="border-t border-gray-200">
                      <dl>
                        ${createDetail('FUR', gineco.fur)} ${createDetail('G', gineco.g)} ${createDetail('P', gineco.p)} ${createDetail('V', gineco.v)} ${createDetail('A', gineco.a)} ${createDetail('C', gineco.c)} ${createDetail('Planificación', gineco.planificacion)}
                      </dl>
                    </div>` : '';

                DOM.historyContent.innerHTML = `
                    <h3 class="text-lg font-semibold text-gray-800 border-b pb-1">Datos de Identificación</h3>
                    <div class="border-t border-gray-200"><dl>${createDetail('Nombre', patient.name)} ${createDetail('Documento', patient.id)} ${createDetail('Fecha de Nacimiento', patient.birthdate)} ${createDetail('Lugar de Nacimiento', patient.history.birthplace)} ${createDetail('Dirección', patient.history.address)} ${createDetail('Teléfono', patient.history.phone)} ${createDetail('Sexo', patient.sex)} ${createDetail('Estado Civil', patient.history.civilStatus)} ${createDetail('Ocupación', patient.history.occupation)} ${createDetail('Escolaridad', patient.history.schooling)} ${createDetail('EAPBS', patient.history.eapbs)} ${createDetail('Régimen', patient.history.regimen)} ${createDetail('Estructura Familiar', patient.history.familyStructure)}</dl></div>
                    <h3 class="text-lg font-semibold text-gray-800 mt-4 border-b pb-1">Consulta Actual</h3>
                    <div class="border-t border-gray-200"><dl>${createDetail('Motivo de Consulta', patient.history.consultReason)} ${createDetail('Enfermedad Actual', patient.history.currentIllness)}</dl></div>
                     <h3 class="text-lg font-semibold text-gray-800 mt-4 border-b pb-1">Antecedentes</h3>
                    <div class="border-t border-gray-200"><dl>${createDetail('Patológicos', patient.history.antecedentes.patologicos)} ${createDetail('Quirúrgicos', patient.history.antecedentes.quirurgicos)} ${createDetail('Farmacológicos', patient.history.antecedentes.farmacologicos)} ${createDetail('Tóxicos', patient.history.antecedentes.toxicos)} ${createDetail('Familiares', patient.history.antecedentes.familiares)} ${createDetail('Psiquiátricos', patient.history.antecedentes.psiquiatricos)} ${createDetail('Transfusionales', patient.history.antecedentes.transfusionales)} ${createDetail('Hemoclasificación', patient.history.antecedentes.hemoclasificacion)} ${createDetail('Alérgicos', patient.history.antecedentes.alergicos)} ${createDetail('Ocupacionales', patient.history.antecedentes.ocupacionales)} ${createDetail('Inmunológicos', patient.history.antecedentes.inmunologicos)} ${createDetail('ETS', patient.history.antecedentes.ets)} ${createDetail('Violencia Intrafamiliar', patient.history.antecedentes.vif)} ${createDetail('Tipo de Vivienda', patient.history.housingType)}</dl></div>
                    ${ginecoHtml}
                `;
                DOM.historyModal.style.display = 'flex';
            };


            // --- EVENT LISTENERS ---
            DOM.addPatientBtn.addEventListener('click', () => { 
                DOM.patientForm.reset(); 
                DOM.patientEditId.value = '';
                DOM.patientModal.style.display = 'flex'; 
                DOM.ginecologicosFieldset.style.display = 'block'; 
            });

            DOM.closePatientModal.addEventListener('click', () => { DOM.patientModal.style.display = 'none'; });

            DOM.patientForm.addEventListener('submit', (e) => { 
                e.preventDefault(); 
                const patientData = getPatientDataFromForm();
                const editingId = DOM.patientEditId.value;

                if (editingId) {
                    const patientIndex = patients.findIndex(p => p.id === editingId);
                    if (patientIndex !== -1) {
                        const originalRecords = patients[patientIndex].records;
                        patients[patientIndex] = { ...patientData, records: originalRecords };
                    }
                } else {
                    if (patients.some(p => p.id === patientData.id)) { 
                        alert('Ya existe un paciente con este número de documento.'); 
                        return; 
                    } 
                    patients.push({ ...patientData, records: [] }); 
                }
                
                saveData(); 
                renderPatientList(); 
                if (editingId) {
                    renderPatientDetails();
                }
                DOM.patientModal.style.display = 'none'; 
            });

            DOM.addRecordBtn.addEventListener('click', () => { 
                if (selectedPatientId) { 
                    DOM.recordForm.reset(); 
                    document.getElementById('record-date').valueAsDate = new Date();
                    DOM.moriskyResult.textContent = 'Seleccione una respuesta para cada pregunta.';
                     DOM.moriskyResult.className = 'mt-4 p-2 bg-gray-100 rounded text-center font-semibold text-gray-700';
                    DOM.adherenceScoreInput.value = '';
                    DOM.recordModal.style.display = 'flex'; 
                } 
            });
            DOM.closeRecordModal.addEventListener('click', () => { DOM.recordModal.style.display = 'none'; });
            DOM.recordForm.addEventListener('submit', (e) => { e.preventDefault(); const patient = patients.find(p => p.id === selectedPatientId); if (patient) { const newRecord = { date: document.getElementById('record-date').value, fragility: parseFloat(document.getElementById('fragility-score').value), fallRisk: parseFloat(document.getElementById('fall-risk-score').value), cognitive: parseFloat(document.getElementById('cognitive-score').value), adherence: parseFloat(document.getElementById('adherence-score').value), cardioRisk: parseFloat(document.getElementById('cardio-risk-score').value) }; if(!patient.records) patient.records = []; patient.records.push(newRecord); saveData(); renderPatientDetails(); renderAggregatedCharts(); DOM.recordModal.style.display = 'none'; } });
            DOM.confirmModalCancel.addEventListener('click', closeConfirmModal);
            DOM.confirmModalConfirm.addEventListener('click', () => { if (confirmCallback) confirmCallback(); });
            
            DOM.moriskyForm.addEventListener('change', calculateMoriskyScore);

            DOM.tabPatientDetails.addEventListener('click', () => { 
                DOM.patientView.classList.remove('hidden'); 
                DOM.aggregatedView.classList.add('hidden'); 
                DOM.scalesFormatView.classList.add('hidden'); 
                DOM.tabPatientDetails.classList.add('active'); 
                DOM.tabAggregatedData.classList.remove('active'); 
                DOM.tabScalesFormat.classList.remove('active'); 
            });
            DOM.tabAggregatedData.addEventListener('click', () => { 
                DOM.patientView.classList.add('hidden'); 
                DOM.aggregatedView.classList.remove('hidden'); 
                DOM.scalesFormatView.classList.add('hidden'); 
                DOM.tabPatientDetails.classList.remove('active'); 
                DOM.tabAggregatedData.classList.add('active'); 
                DOM.tabScalesFormat.classList.remove('active'); 
                renderAggregatedCharts(); 
            });
            DOM.tabScalesFormat.addEventListener('click', () => { 
                DOM.patientView.classList.add('hidden'); 
                DOM.aggregatedView.classList.add('hidden'); 
                DOM.scalesFormatView.classList.remove('hidden'); 
                DOM.tabPatientDetails.classList.remove('active'); 
                DOM.tabAggregatedData.classList.remove('active'); 
                DOM.tabScalesFormat.classList.add('active'); 
            });
            
            DOM.viewHistoryBtn.addEventListener('click', () => { const patient = patients.find(p => p.id === selectedPatientId); if (patient) renderHistoryModal(patient); });
            DOM.editHistoryBtn.addEventListener('click', () => { 
                const patient = patients.find(p => p.id === selectedPatientId); 
                if (patient) {
                    DOM.patientForm.reset();
                    DOM.patientEditId.value = patient.id;
                    populatePatientForm(patient);
                    DOM.patientModal.style.display = 'flex';
                }
            });

            DOM.closeHistoryModal.addEventListener('click', () => { DOM.historyModal.style.display = 'none'; });
            
            DOM.patientSexSelect.addEventListener('change', (e) => {
                DOM.ginecologicosFieldset.style.display = e.target.value === 'Femenino' ? 'block' : 'none';
            });

            window.onclick = (event) => { if (event.target == DOM.patientModal) DOM.patientModal.style.display = "none"; if (event.target == DOM.recordModal) DOM.recordModal.style.display = "none"; if (event.target == DOM.confirmModal) closeConfirmModal(); if (event.target == DOM.historyModal) DOM.historyModal.style.display = "none";};
            
            // --- INITIALIZATION ---
            renderPatientList();
            renderPatientDetails();
            renderAggregatedCharts();
        });
    </script>
</body>
</html>
