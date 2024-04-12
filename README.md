import { createObjectCsvWriter as createCsvWriter } from 'csv-writer';
import * as faker from 'faker';

interface FieldSchema {
    name: string;
    type: 'string' | 'number' | 'datetime' | 'enum';
}

const projectNames = ['ProjectX', 'ProjectY', 'ProjectZ'];
const suitePaths = ['CategoryA', 'CategoryB', 'CategoryC'];
const platforms = ['Linux', 'Windows', 'Mac'];

const schema: FieldSchema[] = [
    { name: 'Suite_name', type: 'string' },
    { name: 'Suite_path', type: 'string' },
    { name: 'run_id', type: 'datetime' },
    { name: 'platform', type: 'enum' },
    { name: 'TestCaseID', type: 'string' },
    { name: 'Execution_status', type: 'string' },
    { name: 'test_case_start_time', type: 'datetime' },
    { name: 'testcase_end_time', type: 'datetime' },
];

function getRandomProjectName() {
    return faker.random.arrayElement(projectNames);
}

function generateRandomData(numRecords: number): any[] {
    const data = [];
    for (let i = 0; i < numRecords; i++) {
        const projectName = getRandomProjectName();
        const suiteNum = faker.datatype.number({ min: 1, max: 10 });
        const suiteName = `${projectName}_SUITE_NAME_${suiteNum}`;
        const suitePath = faker.random.arrayElement(suitePaths);
        const runId = faker.date.recent(30);
        const platform = faker.random.arrayElement(platforms);
        const testCaseID = `${projectName}_${suiteName}_TestCase_${faker.datatype.number(100)}`;
        const executionStatus = faker.datatype.number(10) < 1 ? 'failed' : 'passed'; // Mostly 'passed'
        const testCaseStartTime = new Date(runId.getTime() + faker.datatype.number({ min: 1000, max: 10000 }));
        const testCaseEndTime = new Date(testCaseStartTime.getTime() + faker.datatype.number({ min: 60000, max: 1200000 }));

        data.push({
            Suite_name: suiteName,
            Suite_path: suitePath,
            run_id: runId.toISOString(),
            platform: platform,
            TestCaseID: testCaseID,
            Execution_status: executionStatus,
            test_case_start_time: testCaseStartTime.toISOString(),
            testcase_end_time: testCaseEndTime.toISOString(),
        });
    }
    return data;
}

function writeToCSV(records: any[], fileName: string) {
    const csvWriter = createCsvWriter({
        path: fileName,
        header: schema.map(field => ({ id: field.name, title: field.name })),
    });

    csvWriter.writeRecords(records)
        .then(() => console.log('Data successfully written to CSV'))
        .catch(err => console.error('Failed to write CSV', err));
}

// Generate 100 random records
const records = generateRandomData(100);
writeToCSV(records, 'output.csv');

