import { test, expect } from '@playwright/test';

const loginUrl = 'https://opensource-demo.orangehrmlive.com/web/index.php/auth/login';

test('Log in and filter System Users', async ({ page }) => {
    test.setTimeout(60_000);

    await page.goto(loginUrl);
    await page.getByPlaceholder('Username').fill('Admin');
    await page.getByPlaceholder('Password').fill('admin123');
    await page.getByRole('button', { name: 'Login' }).click();

    await expect(page.getByRole('heading', { name: 'Dashboard' })).toBeVisible();

    await page.getByPlaceholder('Search').fill('Admin');
    await page.getByRole('link', { name: 'Admin' }).click();
    await expect(page.getByRole('heading', { name: 'System Users' })).toBeVisible();

    const filterGroup = (label) => page.locator('.oxd-input-group').filter({ hasText: label });

    await filterGroup('Username').locator('input').fill('Admin');

    await filterGroup('User Role').locator('.oxd-select-text').click();
    await page.getByRole('option', { name: 'Admin', exact: true }).click();

    //const employeeName = filterGroup('Employee Name').locator('input');
    //await employeeName.fill('Muhammad Rehman');
    //await page.waitForTimeout(1000);
    //console.log('Employee name entered:', await employeeName.inputValue());

    await filterGroup('Status').locator('.oxd-select-text').click();
    await page.getByRole('option', { name: 'Enabled', exact: true }).click();

    await page.getByRole('button', { name: 'Search' }).click();

    const results = page.locator('.oxd-table-body');
    await expect(results).toBeVisible();
});