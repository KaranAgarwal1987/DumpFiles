from faker import Faker
import csv
from datetime import datetime, timedelta
import random

# Initialize Faker
fake = Faker()

# Configuration
project_names = ['ProjectX', 'ProjectY', 'ProjectZ']
suite_paths = ['CategoryA', 'CategoryB', 'CategoryC']
platforms = ['Linux', 'Windows', 'Mac']
output_file = 'output.csv'

def generate_test_cases(project_name, suite_name):
    # Generate a larger pool of possible test cases
    num_cases = random.randint(15, 25)  # pool size larger than any single run
    return [f"{project_name}_{suite_name}_TestCase_{random.randint(25, 40)}" for _ in range(num_cases)]

def generate_data():
    records = []
    for project_name in project_names:
        for suite_index in range(1, 4):  # Each project has 3 suites
            suite_name = f"{project_name}_SUITE_NAME_{suite_index}"
            suite_path = random.choice(suite_paths)
            platform = random.choice(platforms)
            # Test cases pool for this suite
            test_cases_pool = generate_test_cases(project_name, suite_name)
            for run_index in range(random.randint(3, 5)):  # Each suite has 3-5 run_ids
                run_id = fake.date_time_between(start_date="-30d", end_date="now")
                # Each run_id uses a subset of the test cases pool
                selected_test_cases = random.sample(test_cases_pool, random.randint(10, 20))
                for test_case_id in selected_test_cases:
                    execution_status = 'failed' if random.randint(1, 10) == 1 else 'passed'
                    test_case_start_time = run_id + timedelta(seconds=random.randint(10, 1000))
                    test_case_end_time = test_case_start_time + timedelta(minutes=random.randint(1, 20))

                    records.append({
                        'Project_name': project_name,
                        'Suite_name': suite_name,
                        'Suite_path': suite_path,
                        'run_id': run_id.strftime('%Y-%m-%d %H:%M:%S'),
                        'platform': platform,
                        'TestCaseID': test_case_id,
                        'Execution_status': execution_status,
                        'test_case_start_time': test_case_start_time.strftime('%Y-%m-%d %H:%M:%S'),
                        'testcase_end_time': test_case_end_time.strftime('%Y-%m-%d %H:%M:%S')
                    })
    return records

def write_to_csv(records, file_name):
    with open(file_name, mode='w', newline='') as file:
        writer = csv.DictWriter(file, fieldnames=records[0].keys())
        writer.writeheader()
        writer.writerows(records)

# Generate and write data
data = generate_data()
write_to_csv(data, output_file)
