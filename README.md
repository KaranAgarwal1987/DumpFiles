import { createObjectCsvWriter as createCsvWriter } from 'csv-writer';
import { faker } from '@faker-js/faker';

type Platform = 'Linux' | 'Windows' | 'Mac';

interface Record {
    Suite_name: string;
    Suite_path: string;
    run_id: string;
    platform: Platform;
    TestCaseID: string;
    Execution_status: 'passed' | 'failed';
    test_case_start_time: string;
    testcase_end_time: string;
}

const projectNames = ['ProjectX', 'ProjectY', 'ProjectZ'];
const suitePaths = ['CategoryA', 'CategoryB', 'CategoryC'];
const platforms: Platform[] = ['Linux', 'Windows', 'Mac'];

function generateRandomData(numRecords: number): Record[] {
    const data: Record[] = [];
    for (let i = 0; i < numRecords; i++) {
        const projectName = faker.helpers.arrayElement(projectNames);
        const suiteNum = faker.datatype.number({ min: 1, max: 10 });
        const suiteName = `${projectName}_SUITE_NAME_${suiteNum}`;
        const suitePath = faker.helpers.arrayElement(suitePaths);
        const runId = faker.date.recent(30);
        const platform = faker.helpers.arrayElement(platforms);
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

function writeToCSV(records: Record[], fileName: string) {
    const csvWriter = createCsvWriter({
        path: fileName,
        header: [
            { id: 'Suite_name', title: 'Suite_name' },
            { id: 'Suite_path', title: 'Suite_path' },
            { id: 'run_id', title: 'run_id' },
            { id: 'platform', title: 'platform' },
            { id: 'TestCaseID', title: 'TestCaseID' },
            { id: 'Execution_status', title: 'Execution_status' },
            { id: 'test_case_start_time', title: 'test_case_start_time' },
            { id: 'testcase_end_time', title: 'testcase_end_time' }
        ],
    });

    csvWriter.writeRecords(records)
        .then(() => console.log('Data successfully written to CSV'))
        .catch(err => console.error('Failed to write CSV', err));
}

// Generate 100 random records
const records = generateRandomData(100);
writeToCSV(records, 'output.csv');
